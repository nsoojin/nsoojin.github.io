---
layout: post
title: "uber/RIBs 유닛 테스트 짜기"
categories: []
tags:
- uber/ribs
- unit tests
type: post
draft: true
published: true
meta: {}
---

[RIBs](https://github.com/uber/RIBs)는 우버에서 개발하고 오픈소스로 공개한 모바일 아키텍처 프레임워크다. RIBs는 독립적인 기능을 담당하는 RIB 덩어리(이하 Riblet)를 트리 구조로 만들어 앱의 상태와 로직을 관리한다. 하나의 Riblet 단위를 구성하는 객체와 역할은 아래와 같다.

<img src="/assets/posts/uber-ribs-diagram.png" />

## RIBs와 객체지향 프로그래밍

위 도식처럼 RIBs 객체는 각자 역할이 뚜렷하게 나뉘어 있다(`Single Responsibility`). 그리고 화살표로 표시돼 있는 각 객체의 input와 output은 프로토콜로 추상화가 돼있어서 따로 떼어내 독립적으로 테스트하기가 좋다(`Dependency Inversion`). 또한 RIBs는 부모와 자식 Riblet의 로직이 트리 구조로 분리(decoupling) 돼있어서 부모 Riblet의 코드 수정을 최소화하면서도 복잡한 자식 Riblet을 새로 만들거나 기능을 수정할 수 있다(`Open-Closed Principle`). 두 달 정도 프로젝트를 해본 바 느낀점은 RIBs 아키텍처를 쓰면 SOLID에서 비중이 크고 꾸준히 따르기 어려운 SRP, OCP, DIP 세 가지 원칙을 어느 정도는 강제적으로 지키게 된다. (물론 어기기도 쉽다. 어떤 아키텍처를 쓰던 내가 코드를 짜기 나름이고 단순히 아키텍처를 도입하는 것만으로 내 코드가 좋아지진 않는다.)

## 무엇을 테스트해야할까

RIBs 아키텍처는 극강의 testability를 지니고 있다. 역할을 잘게 쪼개고 강한 커플링을 끊은 만큼, 높은 커버리지를 달성할 수 있다. 하나의 Riblet당 최소 4+ 개의 테스트 클래스가 필요한데 Xcode 코드 생성 템플릿을 쓰면 매번 만들기 번거로운 유닛 테스트 클래스도 자동으로 생성해준다. 그런데 처음 프로젝트를 할때는 이 많은 테스트 클래스에 뭘 채워 넣어야하는지 몰라 막막했다. 하지만 그동안 코드 리뷰도 받고 기존 코드도 살펴보면서 중요한 규칙을 발견했고 이를 통해 각 클래스를 어떻게 바라보면서 테스트해야 하는지 알았다.

## Router Test: 뷰 라우팅을 제대로 하는지

Router는 비즈니스 로직에 맞춰 자식 Riblet을 뗐다(`attachChild`) 붙였다(`detachChild`) 하는 동작을 검사해야 한다. Router 클래스의 설명에 보면 *'라우터가 자식 라우터를 만들때는 꼭 helper builder를 써야 한다.'*([링크](https://github.com/uber/RIBs/blob/master/ios/RIBs/Classes/Router.swift#L75))는 주석이 있다. 자식 Riblet을 생성할 때 xxBuilder 클래스를 직접 쓰지말고 xxBuildable로 추상화하여 주입 받아야 한다는 말이다. 이렇게 해야 테스트 환경에서 xxBuildableMock을 주입해서 라우터가 자식 Riblet을 생성하는지 검사할 수 있다. Router는 interactor의 호출에 의해 라우팅을 해주는 역할이기 때문에 이것 외에 다른 로직은 없는게 바람직하다.

## Interactor Test: 각종 비즈니스 로직

Interactor는 앱의 비즈니스 로직을 담당하는 곳이라 다른 클래스보다 복잡하고 개발자의 자유도가 높다. 그래서 다른 컴포넌트처럼 유형화하기가 쉽진 않지만 자주 등장하는 몇 가지가 있다.

- Interactor는 view로부터 사용자 입력을 전달받는다. 사용자 입력에 따라 적절한 작업이 실행됐는지 확인하기 위해서 interactor의 view listener 관련 메서드를 호출해준 뒤 필요한 작업이 실행됐는지 Mock 객체로 확인한다.

- Interactor는 자식 interactor에 정보를 전달하기 위해서 reactive 방식을 사용한다. 이렇게 하면 부모와 자식 interactor가 직접적인 의존 관계(direct coupling)에서 벗어날 수 있다. Stream은 어떻게 구현하든 상관은 없는데 보통은 Rx 라이브러리를 쓴다. 참고로 RIBs 아키텍처도 Rx를 쓰고 있어서 이걸 쓰면 굳이 또다른 외부 라이브러리를 추가할 필요 없다. 테스트 환경에서는 스트림을 모킹하여 interactor가 제대로 값을 흘려보내는지 확인한다. 반대로 부모 interactor한테서 주입 받은 스트림에 subscribe해서 작업을 처리해야하는 경우라면 mock 스트림을 주입한 뒤 테스트 케이스에서 값을 흘려보면서 데이터에 맞게 잘 처리하고 있는지 테스트한다.

- Interactor는 router와 presenter(혹은 view)를 자주 호출한다. 뷰를 뗐다 붙였다 하기도 하고 UI를 업데이트하기도 한다. 그래서 router와 presenter 메서드가 제대로 불리는지 확인한다. 이 경우에는 단순히 불렸는지 안불렸는지 확인하는 것보다는 불린 횟수가 정확한지 테스트하는게 좋다. UI 업데이트를 불필요하게 여러번 하지 않는지, 중복으로 뷰 라우팅을 하지는 않는지 확인한다.

## Builder Test: 클래스 생성 및 주입이 제대로 되었는지

Builder는 RIBs 객체를 생성하고 참조점을 연결해주고, 의존성 주입에 필요한 concrete 객체를 생성하는 역할이다. 그래서 Builder의 테스트는 `is` 나 `as?` 를 써서 클래스의 타입을 확인한다.  

## Presenter Test: 뷰모델 생성/변환

데이터 모델을 뷰 모델로 잘 변환했는지 확인한다. 데이터 모델은 Foundation 클래스만 사용하도록 하고 UIKit 클래스는 뷰 모델에서 들고 있도록 한다. 예를 들어 hex 문자열로 넘겨받은 색상 값을 올바른 UIColor로 변환했는지, 또는 Date 객체로 넘겨받은 날짜를 올바른 문자열로 변환했는지 등을 검사한다. 

## View Test: 뷰가 제대로 렌더링 되는지

View는 코드만으로는 유의미한 테스트가 되지 않는다. [스냅샷 테스트](https://github.com/uber/ios-snapshot-test-case) 라이브러리를 쓰면 뷰를 렌더링해서 이미지 파일로 저장해놓고 테스트를 돌릴때마다 새로 렌더링한 뷰의 이미지와 기존 이미지를 비교하는 방식으로 뷰 클래스(UIView, UIViewController)를 검사할수 있다. 언젠가 코드 수정으로 인해 뷰가 바뀐다면 테스트 케이스가 실패하기 때문에 뷰가 잘못된 걸 미리 알아낼 수 있다. 스냅샷 테스트도 유닛 테스트기 때문에 커버리지에 포함되는게 장점이다. 스냅샷 테스트를 추가해서 Riblet의 커버리지를 대략 12~15% 정도 증가시킬 수 있었다. 
