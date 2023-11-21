---
layout: post
title: "의존성 주입 안티패턴: Ambient Context"
categories: []
tags:
- DI
- anti-pattern
- ambient context
type: post
published: true
meta: {}
---

## Ambient Context 정의와 예시

Ambient Context는 static 접근자를 통해 정적 타입의 의존성을 노출시키는 패턴입니다. 이 패턴은 volatile dependency가 의존성 주입 없이 코드베이스 전역에서 사용되도록 만듭니다. 예시를 하나 보겠습니다.

```swift
// 나쁜 코드 예시 ❌
func hoursSince(_ date: Date) -> Double {
  let timeProvider: TimeProvider = DefaultTimeProvider.current
  let now: Date = timeProvider.now
    
  let timeInterval = now.timeIntervalSince(date)
  return timeInterval / 3600.0
}
```

위 예시에서 TimeProvider라는 프로토콜은 현재 시간을 가져오기 위한 추상화입니다. 일반적으로 앱에서 시간이라는 개념을 통제하고 싶을 때(특히 테스트에서) `Date()` 또는 `Date.now`를 직접 호출하지 않기 위해 이와 같은 방법을 씁니다. 이런 추상화를 한 다음, 자칫 `DefaultTimeProvider.current` 와 같이 *기본 구현체(default implementation)*를 함께 제공해서 소비자 객체에서 쉽게 사용하고 싶은 유혹이 생깁니다. 싱글턴과 비슷하긴 하지만, 싱글턴은 인스턴스가 절대 바뀌지 않게 하는 반면에 ambient context는 의존성을 교체할 수 있게 해줍니다.

## Ambient Context가 테스트를 복잡하게 만드는 이유

Ambient Context를 사용하면 테스트도 작성할 수 있습니다. TimeProvider 기본 구현체를 TimeProviderStub으로 교체하여 테스트가 가능합니다.

```swift
// 나쁜 코드 예시 ❌
func testHoursSince() {
  // given
  let currentTime = Date(timeIntervalSince1970: 1647965400)
  DefaultTimeProvider.current = TimeProviderStub(now: currentTime)

  // ⚠️ TimeProvider에 의존하고 있다는 사실이 숨어있음
  let sut = TimeCalculator()

  // when
  // ⚠️ DefaultTimeProvider.current와 hoursSince 사이에 Temporal Coupling이 존재함
  let result = sut.hoursSince(currentTime.addingTimeInterval(-3600))

  // then
  XCTAssertEqual(result, 1, accuracy: 0.001)
}
```

그러나 이 같은 테스트 코드에는 문제가 있습니다. TimeCalculator 객체의 생성자에는 파라미터가 하나도 없어서 TimeProvider를 필수 의존성으로 가지고 있다는 정보가 숨겨져 있습니다. 또한 DefaultTimeProvider.current로 의존성을 주입해줘야 하는 것과 hoursSince 호출 사이에는 Temporal Coupling 문제가 있습니다.

## Ambient Context가 활용된 로깅
Ambient Context가 종종 발견되는 곳은 로깅 기능입니다. 로깅은 코드 전반에 걸쳐 호출되는 경우가 많아 개발자는 로깅에 필요한 의존성을 주입해주는걸 무척 번거롭다고 느낍니다. 가뜩이나 Swift에서는 protocol extension을 통해 추상 인터페이스의 기본 구현을 제공하는 것이 매우 쉽기 때문에 iOS 개발자라면 공통 코드를 재사용하기 위한 방법으로 Ambient Context를 사용하게 됩니다. 이런 구현은 위의 예시 코드보다도 더 간단하기에 유혹에 빠지지가 더 쉽습니다.

```swift
// 나쁜 코드 예시 ❌
public protocol Loggable {
  func log(level: LogLevel, _ message: @autoclosure () -> String)
}

public extension Loggable {
  func log(level: LogLevel, _ message: @autoclosure () -> String) {
    //Firebase Analytics 등의 로깅 라이브러리를 호출
    Analytics.logEvent(message(), parameters: ...)
  }
}
```

## Ambient Context의 문제점
- 의존성이 숨어있다.
- 테스트가 더 어려워진다.
- 상황에 따라 구현체를 바꾸는 것이 어렵다.
- 의존성 생성과 사용 사이에 Temporal Coupling이 생긴다.

### 의존성을 숨겨 코드 스멜을 약화시킨다.

의존성이 숨겨져 있으면 객체가 얼마나 많은 의존성이 있는지 파악하기가 어렵고, 그렇게 되면 객체가 너무 커지고 있다(SRP 위배)는 코드 스멜이 약해져 리팩토링이 필요하다는 신호를 놓치게 됩니다.

### 테스트를 어렵게 만든다.

Ambient Context를 사용하면 테스트를 할 때 전역 상태를 건드려야 하기 때문에 다른 테스트에 영향을 줄 수 있습니다. 병렬 테스트 실행을 할 수 없게 되고, 순차 테스트를 하더라도 제대로 setup이나 teardown을 해주지 않으면 문제가 생길 여지가 있어서 테스트 코드의 유지 보수가 어려워집니다.

### 소비자에 따라 다른 구현체를 제공하기 어렵다.

가령 앱에서 로그를 보내야하는 곳이 하나가 아니라 종류에 따라 다른 로깅 라이브러리를 써야한다면, 이를 구분하기 위해서 아마도 인터페이스에 파라미터를 추가하는 등, 소비자 입장에서 더 복잡한 인터페이스를 제공할 수 밖에 없습니다.

### Temporal Coupling 문제

숨겨져 있는 의존성을 제때 생성해주지 않으면, 기능을 첫 호출하는 시점에서야 잘못됐다는걸 알 수 있습니다. 코드에 문제가 있다면 최대한 빨리 아는 것이 좋습니다.

## Ambient Context 대신 의존성 주입 패턴을 사용하자

Ambient Context 대신 생성자 주입과 decorator 패턴을 통해 Ambient Context 안티패턴을 대체할 수 있습니다. 특히 로깅과 같은 cross-cutting concern에 대해서는 의존성 주입 패턴을 활용하는 것이 유지보수 관점에서 훨씬 좋습니다.

## 📝 Swift 개발자를 위한 DI 강의 사전 신청

의존성 주입의 원칙과 패턴에 대해서 더 깊게 알고 싶으시다면, Ambient Context 안티패턴을 리팩토링 하는 구체적인 방법과 코드 예제에 대해서 궁금하시다면 현재 준비중인 동영상 강의 사전 신청서를 작성해주세요. 강의가 준비되면 가장 먼저 알려드리고 30% 할인 쿠폰을 드립니다.

📎 할인 쿠폰 신청하기: [https://forms.gle/JXwKctVS6XqKoHF76](https://forms.gle/JXwKctVS6XqKoHF76)