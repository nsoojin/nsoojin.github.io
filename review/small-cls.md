---
layout: simple
title: Small CLs
categories: []
tags: []
status: publish
published: true
---

# 작게 쪼개기

## 왜 CL을 작게 해야하는가?
- 리뷰가 빠르다.
- 더 빈틈없는 리뷰를 받을 수 있다.
- 버그가 발생할 가능성이 적다.
- 거절됐을 때 낭비되는 시간이 적다.
- 코드를 머지하기 쉽다.
- 제대로 설계하기 용이하다.
- 리뷰에 의존하지 않고 다른 작업을 하고 있을 수 있다.
- 문제가 생겼을 때 되돌리기 쉽다.

리뷰어는 **CL이 너무 크다는 이유 하나 만으로도 CL을 거절할 수 있다.** 그렇게 될 경우 이미 거대해진 CL을 쪼개는 작업은 힘들고 오래 걸리기 때문에 처음부터 작은 CL 단위로 개발하는 것이 훨씬 쉽고 안전하다.

## 얼만큼이 작은 것인가?
CL에는 **하나의 독립적인 변화**만 있어야 한다. 최소한으로, 단 하나만 포함시킨다. 기능(feature) 중 일부만을 하나의 CL로 만든다. 단 리뷰어가 알아야하는 모든 정보를 담는다. 당연히 CL이 머지된 후에도 프로그램은 잘 돌아가야한다. 너무 작아서도 안된다. 새로운 API를 추가 했다면 그 API를 사용하는 코드도 같은 CL에 작성한다. 

절대적인 기준은 없다. 경험상 100줄 정도가 괜찮은 크기이다. 1000줄은 너무 크다. 또한 단일 파일에서 200줄을 수정한 건 괜찮지만 같은 양이라도 50개 파일에 퍼져 있다면 좋지 않다. 본인이 괜찮다고 생각하는 사이즈보다 조금 더 작게 만들면 리뷰어가 불평할 일이 거의 없다.

## 언제 거대한 CL이 허용되는가?
파일 삭제는 1줄짜리 수정으로 봐도 된다. 툴에 의해 자동 생성된 긴 코드도 마찬가지다. 한줄 한줄 볼 필요없이 파일을 사용할지 말지만 정하면 되기 때문이다. 실제 리뷰어를 기준으로 파일을 나눠서 CL을 만드는 방법도 좋다. CL을 두 개로 쪼개서 각각 다른 사람에게 리뷰 요청을 하고 서로를 참조할 수 있게 설명문에 언급해둔다. 

## 리팩토링 작업은 분리한다
리팩토링 작업은 기능 및 버그 수정과 분리되어야 한다. 클래스 이름을 바꾸거나 위치를 바꾸는 작업을 하나로 만들고, 클래스 내부 버그 수정은 또다른 CL로 만든다. 사소한 지역 변수 이름은 버그 수정이랑 같이 바꿔도 되지만 최종적으로는 리뷰어와 작성자가 논의해서 결정한다.

## 대응되는 테스트 코드는 같은 CL에 포함시킨다
일반적으로는 프로덕션 코드와 테스트 코드를 하나의 CL로 구성한다. 하지만 위에 언급한 리팩토링처럼 테스트 코드 수정만 먼저 반영하는 경우도 있다. 예를 들어 기존의 있던 코드에 새 테스트를 추가하는 경우, 테스트 코드를 리팩토링하는 경우, 통합 테스트(integration test)를 추가하는 경우 등이 있다.

## 빌드를 깨지 말아라
서로 연관성 있는 CL 여러개를 동시에 작업하고 있다면 각각의 CL이 따로 코드에 반영되더라도 빌드가 깨지지 않게 해야한다. 그렇지 않으면 동료 개발자들의 빌드도 같이 깨지게 된다. 

## 작을수록 좋다
CL을 더이상 작게 만들 수 없다는 생각이 들더라도 정말로 그런 경우는 매우 드물다. CL을 작게 만드는걸 연습하는 개발자들은 항상 더 쪼갤수 있는 방법을 잘 찾아낸다. 처음부터 작게 만들 자신이 없다면 먼저 동료들과 얘기를 나눠보고 아이디어를 얻는다. 그래도 방법이 없다면 미리 리뷰어의 동의를 구하되 자신도 더욱 예리한 눈으로 버그를 찾고, 더 꼼꼼하게 테스트를 작성한다.

다음: [리뷰어의 의견에 대처하는 방법]({{ site.baseurl }}{% link review/handling-comments.md %})
