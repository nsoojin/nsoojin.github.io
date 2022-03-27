---
layout: post
title: "2022년 시니어 iOS 개발자 로드맵"
categories: []
tags:
- senior ios developers
- 2022 roadmap
type: post
published: true
meta: {}
---

벌써 11월이라는 것에 놀라 올해를 돌아보니 삶의 중대한 일들을 지나느라 올해는 순수 개발 공부를 많이 못했다. 그래서 밀린 주제들이 많다.

Swift 및 iOS 개발 환경에도 변화의 바람이 불어오고 있다. 개인적으로 관심을 가지고 공부하고 있거나 앞으로 중점적으로 공부할 예정인 주제들을 골랐다.

## 1. 선언형(Declarative) UI

애플의 SwiftUI, 안드로이드의 Compose, 구글의 Flutter가 선언형 UI를 택했다. 올해 회사에서 플러터로 신규 기능 개발을 하면서 선언형 UI를 처음 써봤다. 명령형 UIKit과는 완전히 다른 프로그래밍 방식이었고 꽤 재밌었다. 모바일에서는 리액티브와 선언형이 합쳐진 UI 개발 방식이 대세가 되고 있다.

모바일 개발에서 UI는 핵심 요소다. UI 프레임워크의 동작 방식이 이렇게 크게 바뀌면 앱의 아키텍처도 바뀔 수 밖에 없다. 회사에서 플러터 개발을 할 때 [flutter redux](https://pub.dev/packages/flutter_redux)를 써보니 Redux와 선언형 UI가 잘 맞는다고 느꼈다. SwiftUI로 개발할 때도 Redux 기반 아키텍처가 좋은 시작점이 될 것 같다. 그래서 PointFree [The Composable Architecture](https://www.pointfree.co/collections/composable-architecture)에 관심을 두고 있다.

## 2. 비동기 프로그래밍

Structured concurrency라는 대주제로 Swift 5.5에 다양한 비동기 프로그래밍 관련 문법들이 추가됐다. async, await, AsyncSequence, Task, actor 등등 공부해야할게 수북하다. Completion handler를 멸종시킬 수 있게 된 점이 무엇보다 기쁘다. 조금만 복잡해져도 가독성이 매우 떨어지고 실수하기 쉬운 completion handler를 쓰지 않기 위해 예전부터 Promise와 같은 외부 라이브러리를 가져다 쓰고 있었지만, 써드파티 라이브러리를 팀의 표준으로 삼을순 없었다.

하지만 이제는 훨씬 나은 퍼스트파티 대안이 많이 생겼다. Combine은 기능이 부족하긴하지만 확장해서 쓰기에는 충분하다. Structured Concurrency와 Combine 프레임워크로 훨씬 가독성 높고 실수가 적은 비동기 프로그래밍이 가능해졌다. Completion handler는 이제 뒤도 안돌아보고 보내줄때다.

## 3. 테스팅

자동화 테스팅은 프로그램의 실패와 오류를 최대한 빨리 파악하기 위해 중요하다. 테스팅은 단기간에 끝나는 공부가 아니라 커리어 내내 공부하고 경험치를 쌓아야 하는 분야인 것 같다. 자동화 테스트는 두가지 방면으로 공부가 필요한데 테스트 가능한 코드를 만드는 것과 작성한 테스트를 잘 운영하는 것이다. 

테스트 가능한 코드를 만들기 위해선 디자인 패턴 공부가 도움이 많이 됐다. 켄트 벡의 [구현 패턴](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9788960770317), 마틴 파울러의 [리팩터링](http://www.kyobobook.co.kr/product/detailViewKor.laf?ejkGb=KOR&mallGb=KOR&barcode=9791162242742&orderClick=LAG&Kc=)을 계속 참고하게 될 듯 하다. 그리고 새 프로젝트에 유닛 테스트, 스냅샷 테스트, 통합 테스트 등 차근차근 쌓아가고 있기 때문에 앞으로 앱과 코드를 운영 하면서 노하우를 쌓기를 기대하고 있다. 최근에 한글로 번역돼서 나온 [단위 테스트](http://www.yes24.com/Product/Goods/104084175)라는 책이 평이 좋던데 꼭 봐야겠다.

## 4. 스위프트 패키지

우리 팀에서 스위프트 패키지를 적극 활용하고 있는데 지금까지의 경험은 매우 좋다. 우리는 계층화된 모듈로 코드를 분리해서 개발하기 때문에 관리해야할 라이브러리가 많은데 xcodeproj에서 발생하는 코드 충돌이 없어서 한결 편하다. 리소스도 추가 가능해져서 사실상 모든걸 할 수 있다. 이젠 스위프트 패키지를 지원하지 않는 외부 라이브러리도 거의 없다. 아직 엑스코드 버그 몇 개 때문에 가끔 불편하긴 한데 심각한 정도는 아니다.

좀 더 공부가 필요한 부분은 SPM이 알아서 라이브러리 타입을 다이나믹과 스태틱 중에 선택해준다는건데 아직 정확히 동작을 파악할 시간적 여유가 없었다. 다이나믹 라이브러리 갯수는 관리 대상이기 때문에 동작을 이해하고 써야한다.

## 5. 컬렉션뷰

정말 오랜만에 테이블뷰/컬렉션뷰 코딩을 하게 돼서 최근에서야 UICollectionViewCompositionalLayout을 공부하고 적용했는데 예상만큼 강력했다. 제대로 구현하려면 은근 골치아팠던 Orthogonal 레이아웃을 쉽게 구현할 수 있다. Diffable data source도 앞으로 유용하게 쓰일거 같다. 애플이 제공한 [샘플코드](https://developer.apple.com/documentation/uikit/views_and_controls/collection_views/implementing_modern_collection_views)만 봐도 기능이 정말 많아서 익숙해지는데에 시간이 좀 걸릴듯 싶다. 이제 테이블뷰는 deprecate돼도 이상하지 않을거 같다.

## 6. 지속적 통합/배포

[Trunk Based Development](https://trunkbaseddevelopment.com)를 도입하고 자동화 테스트를 운영하고 있는 우리 팀에서는 CI/CD가 특히 중요하다. 지속적 통합과 지속적 배포를 떼어놓고 앱 개발을 할 수는 없는데 환경을 구축하고 운영하는게 결코 쉽지 않은 일이다. 툴은 많은데 사용이 편하면 기능 확장이 쉽지 않고 자유도가 높은건 구축과 운영 비용이 높다. 엑스코드 클라우드가 좋은 대안이 되길 기대하고 있다.

## 7. 도메인 지식

현재 회사에서 동영상과 이미지를 아주 많이 다루는데 미디어 포맷 관련 지식이 부족해서 한계를 느끼고 있다. 몇 달 전에는 mp4 동영상의 프레임을 시간 역순으로 불러오려고 이틀을 붙잡고 있다가 코덱 특성상 불가능한 일이었다는걸 배웠다. 코덱이 뭔지도 이때 처음 공부했다. 이제는 어깨 너머로 익힌게 쌓여 삽질하는 정도는 아니지만 프로젝트를 잘 이끌기 위해 더 깊은 도메인 지식이 필요하다. mp4, jpg, webp, webm 등을 공부할 예정이다.

그랩페이를 개발할때는 금융에 대한 지식이 도움이 됐고 웹툰을 개발할때는 컨텐츠 소비에 대한 경험과 관심이 도움이 됐다. 도메인 지식을 몰라도 어느정도 앱은 만들수 있겠지만, 시니어로 갈수록 회사의 도메인에 대한 공부와 관심이 더욱 중요하지 않나 싶다. 단순히 요구사항만 맞추는게 아니라 서비스의 방향도 책임지는 개발자라면.

## 8. 모듈 아키텍처

모듈화는 [확장 가능한 앱 아키텍처를 구축](https://www.youtube.com/watch?v=KHru8XQyGFA)하기 위해 반드시 해야할 일이다. 그랩에서 엄청난 규모의 코드가 모듈화되어 돌아가는걸 봤고, 거기서 엿본 패턴과 원칙과 경험을 토대로 더 개선해서 현재 프로젝트에 적용하고 있다.

모듈끼리 느슨하게 결합하고, 단방향으로만 참조하는 계층 구조를 구축해서 서비스의 특성과 팀의 구조에 맞춰 발전시키고 있다. 아키텍처를 상황에 맞춰 꾸준히 진화시키는 값진 경험을 쌓을 것이다. 이 목록에 있는 것들을 배우고 적용시키면서 맞이할 2022년을 기대해본다.

-

모듈 아키텍처의 원리와 구축, 비동기 코드 테스팅, 자동화 테스트 작성과 운영 노하우, 스위프트 패키지로 모듈 관리하는 법, Combine 활용법 등을 알고 싶다면 제 강의 [[The Red : 노수진] 슈퍼앱 운영을 위한 확장성 높은 앱 아키텍처와 구축](https://fastcampus.co.kr/dev_red_rsj?utm_source=soojin-blog&utm_medium=2022roadmap&utm_campaign=soojin)을 좋은 시작점으로서 추천합니다.







