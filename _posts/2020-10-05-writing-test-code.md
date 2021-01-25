---
layout: post
title: "테스트 코드 작성하면 좋은 점"
categories: []
tags:
- tests
type: post
published: true
meta: {}
---

코드를 수정하고나서 풀리퀘를 올렸는데 테스트가 실패한 것을 보고 내가 놓친 부분을 찾아서 수정하는 경험을 했었고 테스트의 가치를 피부로 느낄 수 있었다. 우리 팀은 커버리지를 KPI로 잡는다. 업무 시간의 20%는 테스트 작성, 빌드 타임 개선, 모듈화 등의 기술 과제에 할당한다. 테스트 코드를 작성하고 유지하는 경험을 통해 느낀 점이 있다.

### 더 나은 아키텍처를 좇게 한다

테스트가 어려운 코드는 좋지 않은 구조일 가능성이 매우 높다. 테스트 가능한 코드는 의존성에 대한 고민이 녹아있다. 특히 의존성 역전 원칙을 철저히 지키면서 소스 코드의 의존성을 정리하고 관리해야 하는데 이에 대해서는 엉클밥이 [의존성 규칙dependency rule](https://khalilstemmler.com/wiki/dependency-rule/)을 통해 잘 설명하고 있다.

### 내 코드 개밥먹기dogfooding

테스트를 작성하면 본인이 짠 코드를 사용자로써 직접 체험해볼 수 있다. 마치 집을 짓고 내부를 꾸미다가 밖으로 나와서 외부에서는 어떻게 보이는지 확인하는 것이다. 창문은 어떻게 생겼으며 문은 어떻게 열고, 입구는 어디에 있는지 등등. 창문과 입구는 public API와 같다. 집 내부는 private 요소들이다. 테스트에서 객체의 동작을 검사할 때는 public API를 쓴다. 객체를 직접 생성도 해보고 설정을 하고 public 메서드를 호출 하거나 프로퍼티에 접근해본다. 그러면서 내가 만든 코드의 사용성을 확인해볼 수 있다. API가 엉성하면 테스트를 작성하기도 까다롭고 고통스럽다. '이런 것까지 테스트해야돼?' 또는 '이 케이스는 왜 이렇게 테스트하기가 어렵지?' 등등 여러 상황을 맞닥뜨린다. 따라서 API의 사용성에 신경쓸 수 밖에 없고, 신경 쓰다보면 더 심플하고 직관적인 API를 만들 수 있게 된다. 더 심플하다는건 여러가지 의미가 있는데 내 생각엔 public 메서드나 프로퍼티의 갯수가 적은게 특히 중요한 것 같다.

### 심플하고 직관적인 API 설계

기능 개발 → 테스트 작성 → 기능 수정 → 테스트 수정의 과정을 반복하다보면 API를 만들때 설계 단계에서 미리 큰 그림을 그려볼 수 있는 근육이 생기는 것 같다. 가령 기능 수정을 조금 하고나니 기존 테스트를 전부 폐기하고 새로 짜야할때가 발생한다. 이러면 설계가 처음부터 좋지 않았을 가능성이 높다. 쉽게 바뀌지 않는 정책적인 것을 public으로 공개하고, 상세 구현부는 숨기거나 외부로 옮겨야 한다. 설계가 잘못되면 테스트를 작성하는게 불가능하거나, 어찌저찌 되더라도 유지하는게 괴롭다. 이런 일이 반복되면 객체의 구조나 역할, 테스트 가능성testability을 고려할 수 밖에 없다. 무엇을 이 객체 안에 넣어야하며 무엇을 빼야하고, 어떻게 의존성을 둬야할지 고민하게 한다. 어떻게 코드를 설계해야할지 막막하거나 어떤 패턴이 더 나은지 판단하기 어렵다면 테스트 코드를 기준으로 삼는 것도 좋은 선택인 것 같다.

### 의미있는 테스트를 만들고 유지할 수 있게 팀의 역량 증가

안타깝지만 그냥 어느날 갑자기 테스트 코드를 짜고 싶다고 짤 수 있는건 아니다. 테스트를 짤 수 있는 코드 구조와 환경이 갖춰져 있어야 한다. 따라서 테스트를 짤 수 있는 아키텍처를 도입하고 유지해야 한다. 애플의 MVC는 테스트 짜기가 너무 힘들다. UI를 만들지 않고는 유저 액션 이벤트를 발생시킬수 없다는 문제점이 있다. 또한 뷰컨트롤러는 라이프사이클 관련 메서드가 복잡하고 갯수가 많아서 테스트가 까다롭다. 그리고 의존성을 주입하는 것 마저도 간단하지가 않다. 그래서 MVVM, VIPER, RIBs 등의 아키텍처가 생겨났다. 그러나 아키텍처를 도입한다고 끝이 아니다. 아키텍처는 은탄환이 아니기 때문에 도입하더라도 팀이 처한 상황과 해결해야 하는 문제에 따라 끊임없이 변형된다. 그래서 시간이 지나도 테스트가 가능한, 그리고 테스트를 짜는게 힘들지 않고 재밌는 구조를 유지하는 기술 리더십이 중요하다. 또한 테스트를 짜는 일이 힘들거나 생산성이 떨어지지 않는 환경을 구축하고 적절한 툴을 도입하거나 만드는 팀워크도 중요하다.