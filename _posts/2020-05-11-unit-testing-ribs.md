---
layout: post
title: "uber/RIBs 유닛 테스트 짜기"
categories: []
tags:
- uber/ribs
- unit tests
type: post
published: true
meta: {}
---

[RIBs](https://github.com/uber/RIBs)는 우버에서 개발하고 오픈소스로 공개한 모바일 아키텍처 프레임워크다. RIBs 프레임워크는 앱의 복잡한 상태 관리와 비즈니스 로직을 RIBs 덩어리(이하 Riblet)로 분리한 뒤 [트리 구조](https://github.com/uber/RIBs/wiki#state-management)로 연결시킨다. 하나의 Riblet 단위를 구성하는 객체와 각각의 역할은 아래와 같다.

<img src="/assets/posts/uber-ribs-diagram.png" />

## RIBs와 객체지향 프로그래밍

위 도표처럼 각 RIBs 객체는 역할이 뚜렷하게 나눠져 있다(`Single Responsibility`). 그리고 화살표로 표시돼 있는 각 객체의 input와 output은 프로토콜로 추상화 돼있어서(`Dependency Inversion`) 따로 떼어낸 후 mocking 기법을 통해 독립적으로 테스트하기 좋다. 또한 부모와 자식 Riblet은 트리 구조로 분리(decoupling) 돼있어서 부모 Riblet의 코드 수정을 최소화하면서도 복잡한 자식 Riblet을 새로 만들거나 수정할 수 있다(`Open-Closed Principle`). 두 달 정도 프로젝트를 해본 바 느낀점은 RIBs 아키텍처를 쓰면 SOLID에서 비중이 크고 꾸준히 지키기 어려운 SRP, OCP, DIP 세 가지 원칙을 반강제적으로 지키게 된다. *물론 어기기도 쉽다. 어떤 아키텍처를 쓰든 개발자가 코드를 짜기 나름이고 단지 아키텍처를 도입하는 것만으로 내 코드가 좋아지진 않는다.*

## 무엇을 테스트 해야할까

RIBs 아키텍처를 구성하는 객체들은 단위 테스트하기 정말 용이하다. 역할이 명확하고 잘게 나뉘어있고 부모와 자식 Riblet이 약하게 커플링된 만큼, 높은 커버리지를 달성할 수 있다. Riblet당 최소 4+ 개의 테스트 클래스가 필요한데 [Xcode의 코드생성 템플릿](https://github.com/uber/RIBs/tree/master/ios/tooling)을 쓰면 매번 만들기 번거로운 유닛 테스트 클래스와 보일러플레이트 코드까지 자동으로 생성해준다. 그런데 처음 프로젝트를 할때는 이 많은 테스트 클래스에 뭘 채워 넣어야하는지 몰라 막막했다. 하지만 코드 리뷰도 받고 기존 코드도 살펴보면서 규칙을 발견했고 이를 통해 각 클래스의 역할과 목적에 따라 어떤 방식으로 테스트 해야하는지 터득했다.

## Router Test: 자식 Riblet 라우팅

Router의 테스트는 비즈니스 로직에 맞춰 자식 Riblet을 뗐다(`attachChild`) 붙였다(`detachChild`) 하는 동작을 검사해야 한다. Router 객체 설명에 보면 *'라우터가 자식 라우터를 만들때는 꼭 helper builder를 써야 한다.'*([링크](https://github.com/uber/RIBs/blob/master/ios/RIBs/Classes/Router.swift#L75))는 주석이 있다. 자식 Riblet을 생성할 때 xxBuilder 클래스를 직접 쓰지말고 xxBuildable로 추상화하여 주입 받아야 한다는 말이다. 이렇게 해야 테스트 환경에서 xxBuildableMock을 주입해서 라우터가 자식 Riblet을 제대로 생성했는지 검사할 수 있다. Router는 interactor의 요청에 따라 라우팅을 대신해주는 역할이기 때문에 이것 외에 다른 로직은 없는게 바람직하다.

## Interactor Test: 각종 비즈니스 로직

Interactor는 앱의 비즈니스 로직을 담당하는 부분이라 다른 클래스보다 복잡하고 개발자의 자유도가 높다. 그래서 다른 컴포넌트처럼 유형화하기가 쉽진 않지만 자주 사용되는 몇 가지가 있다.

- Interactor는 자식 interactor에 정보를 전달하기 위해 리액티브 프로그래밍 방식을 사용한다(참고: [공식 문서](https://github.com/uber/RIBs/wiki#communication-between-ribs)). 리액티브 스트림을 활용하면 부모와 자식 interactor가 직접적인 의존 관계(direct coupling)를 맺지 않을 수 있다. 스트림은 어떻게 구현하든 개발자의 자유지만 보통은 [RxSwift](https://github.com/ReactiveX/RxSwift)를 쓰면 좋다. RIBs 아키텍처도 Rx를 쓰고 있어서 이걸 쓰면 굳이 리액티브 라이브러리를 여러개 추가할 필요가 없다. 테스트 환경에서는 스트림을 mocking해서 interactor가 제대로 값을 내보내는지 확인할 수 있다. 반대로 부모 interactor한테서 주입 받은 스트림에 subscribe해서 작업을 처리해야하는 경우라면 마찬가지로 mock 스트림을 주입한 뒤 테스트 케이스에서 값을 바꿔가면서 데이터에 맞게 잘 처리하고 있는지 검사한다.

- Interactor는 view로부터 사용자 입력을 전달받는다. 사용자 입력에 따라 적절한 작업이 실행됐는지 확인하기 위해서 interactor의 view listener 관련 메서드를 테스트 케이스에서 직접 호출해준 뒤 필요한 작업이 실행됐는지 mocking된 의존성 객체를 확인한다.

- Interactor는 router와 presenter(혹은 view)를 자주 호출한다. 뷰를 뗐다 붙였다 하기도 하고 UI를 업데이트하기도 한다. 그래서 router와 presenter 메서드가 의도에 맞게 불리는지 확인한다. 이 경우에는 단순히 불렸는지 안불렸는지 확인하는 것보다는 불린 횟수를 정확히 검사하는게 좋다. UI 업데이트를 불필요하게 여러번 하는지 확인할 수도 있고 중복으로 뷰 라우팅을 하지 않는지도 확인한다. (참고: [공식 튜토리얼 Mock 객체](https://github.com/uber/RIBs/blob/master/ios/tutorials/tutorial2/TicTacToeTests/TicTacToeMocks.swift))

## Builder Test: Concrete 클래스 생성과 주입

Builder는 RIBs 객체를 생성하여 참조점을 연결해주고, 의존성 주입에 필요한 concrete 클래스를 생성하는 역할이다. 따라서 `is` 나 `as?` 를 써서 올바른 클래스 타입이 생성됐는지 확인해주는 방식으로 동작을 검사한다.

## Presenter Test: 뷰 모델 생성 로직

데이터 모델이 뷰 모델로 잘 변환됐는지 확인한다. 데이터 모델은 UIKit 클래스를 쓰지 않도록 만들고, 뷰를 구성할때 필요한 UIKit, Core Graphics 타입 등은 뷰 모델에서 가지고 있는게 좋다. 예를 들어 데이터 모델은 hex 문자열로 색상 값을 가지고 있고 presenter에서 이를 UIColor로 변환을 한다. 또는 데이터 모델이 Date 타입으로 날짜를 가지고 있고 presenter에서 DateFormatter를 통해 문자열로 변환해서 뷰 모델에 저장한다. 이같은 변환 로직을 검사해야 한다.

## View Test: 뷰의 렌더링

View는 코드만으로는 유의미한 테스트를 하기 어렵다. [스냅샷 테스트](https://github.com/uber/ios-snapshot-test-case) 라이브러리를 쓰면 뷰를 렌더링해서 이미지 파일로 저장해놓고, 테스트를 돌릴때 뷰를 새로 렌더링해서 기존의 이미지 파일과 비교하는 방식으로 뷰 클래스(UIView, UIViewController)를 검사할수 있다. 만약에 코드 수정으로 인해 뷰가 바뀐다면 테스트 케이스가 실패하기 때문에 뷰가 잘못된 걸 미리 알아낼 수 있다. 스냅샷 테스트도 유닛 테스트기 때문에 커버리지에 포함되는게 장점이다. 스냅샷 테스트를 추가해서 Riblet의 커버리지를 대략 12~15% 정도 증가시킬 수 있었다.
