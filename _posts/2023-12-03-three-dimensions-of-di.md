---
layout: post
title: "의존성 주입을 하면 잃게 되는 것"
categories: []
tags:
- dependency injection
- DI scope
type: post
published: true
meta: {}
---

### (부제: 객체는 잃지만 개발자가 얻는 것)

의존성 주입을 하면 프로그램에 꼭 필요한 중요 역할이 여러 클래스로 분산됩니다. 의존성을 생성하는 그 막강한 권한이 소비자 객체에서 다른 곳으로 옮겨갑니다. 처음에는 객체가 자신의 의존성을 스스로 생성하고 관리할 권한을 잃는다는게 부정적으로 느껴질 수 있지만 이렇게 함으로써 **개발자가 그 권한을 얻게 됩니다.**

의존성을 생성하는 작업을 *객체 합성(object composition)*이라고 부릅니다. 의존성 주입을 하면 객체가 잃게 되는 대표적인 권한입니다. 하지만 이게 전부가 아닙니다. 의존성을 생성하지 못하니 그것의 *생애(object lifetime)*도 관리하지 못합니다. 개발자가 객체을 합성하고 수명을 통일해서 관리할 수 있는 권한을 가지게 되면, 의존성이 소비자 객체에 주입되기 전에 *가로채서(intercept)* 무언가를 할 수 있게 됩니다. 다시 말해 객체가 이런것들을 잃는다는 것은 반대로 개발자가 그걸 얻게 된다는 말압니다. 이 3가지에 대해 간략히 살펴보겠습니다.

## 객체 합성 Object Composition

앱을 개발한다는 건 뜯어보면 여러 기능, 모듈, 객체들을 우리가 원하는 방식대로 조합하는 작업입니다. 레고 조각들을 모아서 원하는 모양을 만드는 것처럼요. 앱 개발자는 이 과정에서 유연성과 확장성을 원합니다. 기존의 코드는 최대한 수정하지 않고 이리저리 조합하여 새로운 요구사항에 대응할 수 있는게 제일 좋습니다. 의존성 주입을 하면 앱의 최상단에서 객체들을 원하는대로 합성할 수 있습니다. 의존성 주입을 하는 가장 큰 이유이기도 합니다. 

## 객체 생애 관리 Object Lifetime Management

더이상 의존성 생성을 직접 하지 않게 된 객체는 그 의존성의 생애를 관리할 수도 없습니다. 의존성을 주입받은 객체는 그 의존성이 언제 어떻게 생성됐는지, 언제 해제되는지 알 방법이 없습니다. 하지만 모르는게 오히려 더 좋습니다. 신경쓸게 적어지니 훨씬 간단해집니다. 반대로 개발자가 이를 관리하게 됩니다. 앱의 객체들이 몇 개나, 얼마나 오래 살아있을지, 또 누구에게 넘겨줄지 결정하고 관리해줄 수 있는 권한이 생깁니다.

스위프트에서는 ARC가 객체의 수명을 관리해줍니다. 소비자 객체가 의존성을 강하게 참조하고 있기만 하면 자신이 살아있는 동안에는 마음껏 사용할 수 있습니다. 아래 코드와 같이 개발자가 레포지토리 인스턴스를 하나만 만들어서 여러 서비스 객체에 주입해 줄 수 있습니다.

```swift

let authRepository: AuthenticationRepository = AuthenticationRepositoryImp()

// 하나의 의존성 객체를 여러 소비자에게 주입
let profileService: ProfileService = ProfileServiceImp(auth: authRepository)
let feedService: FeedService = FeedServiceImp(auth: authRepository)
```

## 가로채기 Interception

의존성에 대한 권한을 개발자가 쥐게 되면 소비자 객채에 의존성을 넘겨주기 전에 조작을 가할 수 있습니다. 이런 권한을 실제 코드로 구현한 것이 데코레이터 패턴입니다. 데코레이터 패턴을 사용하면 가로채기를 활용해 **단일 책임 원칙**을 지킬 수 있습니다. 특히 횡단 관심사(cross cutting concern)를 처리할 때 매우 유용합니다. 예를 들어 앱의 주요 API를 모니터링하고 싶습니다. 성공/실패 여부와 네트워킹 소요시간을 측정해서 서비스 로그로 보내려고 합니다. 이와 같은 성능 측정 코드를 API 서비스 객체에 바로 구현하기 보다는 모니터링을 담당하는 데코레이터를 만들고, 이 데코레이터 객체를 주입해줄 수 있습니다. 이렇게 하면 서비스의 고유 로직과 기타 로직을 분리할 수 있습니다.

```swift
protocol PurchaseService {
  func purchase(_ productID: String) async throws
}

// 데코레이터 패턴으로 성능/에러 로깅 구현
final class PurchaseServiceMonitoringDecorator: PurchaseService {
  private let decoratee: PurchaseService
  private let logger: AppAnalytics
  private let clock: any Clock
  
  func purchase(_ productID: String) async throws {
    do {
      let duration = try await clock.measure {
        try await self.decoratee.purchase(productID)
      }
      self.logger.log(AppEvent.purchase, [AppEvent.durationKey: duration])
    } catch {
      self.logger.logError(error)
      throw error
    }
  }
}
```

## 결론

의존성 주입이라는 개념이 처음 생겨났을 때는 오로지 객체 합성의 문제를 포함하고 있었는데 그 개념은 점점 확대되어 오늘날에는 위 3가지를 모두 다루는 것으로 발전해왔습니다. 비록 객체 합성이 없으면 생애 관리도 필요 없고 가로채기도 할 수 없으므로 제일 중요한 부분이긴 하지만 나머지 둘도 잊으면 안됩니다. 객체 생애 관리에 대해서 염두하고 있어야 예기치 못한 사이드 이펙트를 방지할 수 있고, 특히 가로채기야 말로 개발자가 의존성 주입을 통해 비로소 얻을 수 있는 달달한 열매라는 사실을 기억하시길 바랍니다.

--

# 📝 Swift 개발자를 위한 DI 강의 사전 신청

객체 합성, 수명 관리, 가로채기 권한을 가지고 앱 개발자가 누릴 수 있는 강력한 설계 능력을 확인해보고 싶으시다면 현재 준비중인 동영상 강의 사전 신청서를 작성해주세요. 강의가 준비되면 가장 먼저 알려드리고 30% 할인 쿠폰을 드립니다.

📎 할인 쿠폰 신청하기: [https://forms.gle/JXwKctVS6XqKoHF76](https://forms.gle/JXwKctVS6XqKoHF76)