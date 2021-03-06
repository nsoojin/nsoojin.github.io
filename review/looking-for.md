---
layout: simple
title: What to look for in a code review
categories: []
tags: []
status: publish
published: true
---

# 코드 리뷰에서 중점적으로 볼 점

## 디자인
코드 리뷰를 할 때 가장 중요한 것은 전체적인 코드의 설계다. 각 부분이 잘 디자인 되어 있는가? 해당 코드베이스에 추가해야 하나 아니면 다른 라이브러리에 추가해야 하나? 지금 시점에 기능을 추가하는 것이 옳은가?

## 기능
작성자가 의도한 대로 코드가 동작하는가? 코드 동작이 사용자에게 유익한가? "사용자"는 프로그램의 소비자일수도 있고 코드를 *사용할* 다른 개발자일 수도 있다. 리뷰어는 엣지 케이스도 고려해야 한다. 특히 **UI 변경**이 있는 경우에는 기능이 잘 동작하는지 확인한다. 

또한 CL에 **병행 작업(concurrency)**이 있는 경우 교착 상태나 경쟁 상태가 발생하지 않는지 유심히 살펴야 한다. 이런 종류의 문제는 코드를 실행해도 찾기가 쉽지 않기 때문에 고민을 많이 해야하고 만약 위험이 발견되는 경우 병행 작업을 제거하는 편이 낫다.

## 복잡도
코드를 더 간단히 할 수는 없는가? 라인별, 함수별, 클래스별로 모두 따져보라. 복잡한 코드는 이해하는데 오래 걸리고, 사용하고 유지 보수할 때 버그가 발생할 가능성이 높다. **오버엔지니어링** 또한 불필요하게 복잡도를 증가시킨다. 리뷰어는 오버엔지니어링이 발생하지 않도록 각별히 유의하고 미래의 문제는 실제로 발생했을 때 해결하도록 한다.

## 테스트
유닛 테스트, 통합 테스트, E2E 테스트를 작성하도록 한다. [긴급 상황]({{ site.baseurl }}{% link review/emergencies.md %})이 아닌 일반적인 상황에서는 프로덕션 코드와 테스트 코드가 같은 CL에 들어있어야 한다. 보통 테스트 코드를 테스트하는 코드는 만들지 않기 때문에 테스트가 적합한지 여부는 반드시 사람이 확인해야 한다. 코드가 깨지면 테스트도 실패하는지, 적절한 단위로 나뉘어져 있는지 확인해본다. 테스트 코드도 유지 보수가 필요한 코드이기 때문에 이 또한 불필요하게 복잡하지 않은지 본다.

## 이름
변수, 클래스, 메서드 이름이 명료하게 지어졌는가? 좋은 이름은 대상이 무엇인지, 무얼 하는지 충분히 설명하면서도 읽기 어려울 정도로 길지 않다.

## 주석
주석이 명료하고 도움이 되는가? 정말 필요한 주석만 있는가? 유용한 주석은 코드가 *어떤* 일을 하는지가 아니라 **왜 존재하는지**를 담고 있다. 코드만 봐서는 어떤 일을 하는지 이해가 잘 안 된다면 코드를 더 간단하게 고쳐야한다. 정규 표현식이나 복잡한 알고리즘에 대해서는 예외로 어떤 일을 하는지를 적어도 좋지만 일반적으로는 코드에 담을 수 없는 정보를 주석에 남기는 편이 좋다.

## 스타일
구글에서 쓰이는 메이저 언어들은 모두 스타일 가이드가 있다. 스타일 가이드에 없는 사안은 개인의 선호에 맡기기 때문에 이걸로 코드 리뷰를 지연시키지는 말아야 한다. 그리고 코드 스타일 변경은 별도 CL로 리뷰 받아야 한다. 스타일 변경과 코드 수정을 한꺼번에 하게 되면 머지와 롤백이 힘들어지고 리뷰를 하기도 어렵다.

## 문서
코드에 대응되는 문서를 제대로 최신화 시켰는가? CL의 변경사항이 빌드, 테스트, 배포 방식을 바꾸게 되면 꼭 문서도 함께 업데이트한다.

## 모든 줄
리뷰어의 자격으로 CL의 모든 줄을 리뷰해야 한다. 자동 생성 코드나 규모가 큰 자료구조 같은 경우는 훑어만 봐도 괜찮지만 사람이 작성한 코드는 반드시 주의깊게 살핀다. 만약 코드가 보기 어려워서 리뷰가 더디면 작성자한테 설명을 요구한다. 구글은 훌륭한 개발자를 뽑기 때문에 당신이 이해할 수 없는 코드는 다른 개발자도 이해하지 못한다. 만약 당신이 올바르게 리뷰할 수 없는 특수한 부분(보안, 병렬, 접근성, i18n 등)이 있다면 자격이 있는 리뷰어를 지정해준다.

## 문맥
수정된 일부분만 보지말고 전체적인 문맥을 파악한다. 수정사항이 포함된 함수나 클래스 전체를 살펴라. 수정은 단 몇 줄이었지만 알고보니 100줄짜리 함수에 들어 있을 수도 있다. **절대로 전체 코드 품질을 떨어뜨리는 CL을 승인하지 않는다.** 보통 작은 변화가 모여서 전체 복잡도를 증가시키기 때문에 새로 추가 되는 코드에는 미미한 복잡도도 허용하지 말아라.

## 좋은 점
CL을 리뷰하다가 좋은 점을 발견하면 작성자에게 언급한다. 코드 리뷰를 하다보면 실수만 지적할 때가 있는데 좋은 부분에는 칭찬과 고마움을 아끼지 않는다. 멘토링 측면에서 잘못보다는 잘한 점을 알려주는 것이 더 값지다.

## 요약
코드 리뷰를 하면서 다음 항목들이 잘 지켜졌는지 확인한다.
- 코드가 잘 설계되었는지
- 사용자에게 유용한 기능을 제공하는지
- UI 변경이 합리적이고 보기 좋은지
- 병렬 작업이 안전하게 실행되는지
- 불필요하게 복잡하진 않은지
- 지금 당장 필요없는 기능을 개발하진 않았는지
- 유닛 테스트가 적절한지
- 테스트는 잘 설계되었는지
- 모든 이름을 잘 지었는지
- 주석은 명료하고 유용한지, *무엇*보다는 *왜*를 나타내는지
- 문서에 반영이 되었는지
- 스타일 가이드를 따르는지

리뷰어는 **모든 줄을 리뷰**하고, 전체적인 **문맥**을 살피고, **코드 품질을 개선**시키고, 작성자가 **잘한 점**은 꼭 칭찬한다.

다음: [리뷰 중인 CL 헤쳐보기]({{ site.baseurl }}{% link review/navigating-cl.md %})
