---
layout: simple
title: Review
categories: []
tags: []
status: publish
published: true
meta: {}
---

# 구글의 코드 리뷰 가이드

아래 글은 [Google Code Review Developer Guide](https://google.github.io/eng-practices/review/)의 **요약 번역본**입니다.

## 용어 정의
- `CL`: "changelist"의 약어. 소스 코드 관리(version control)에 반영되었거나 리뷰를 받고 있는 코드의 집합체 단위.
- `LGTM`: "Looks Good To Me"의 약어. 리뷰를 통과시킬때 리뷰어가 남기는 말이다.

그 외 자주 등장하는 단어들은 아래와 같이 통일해서 사용했습니다.
- `CL author`: 작성자
- `Reviewer`: 리뷰어
- `Code health`: 코드 퀄리티

## 소개
이 글은 구글의 개발자들이 시행하는 코드 리뷰의 과정과 원칙을 담고 있다. 크게 두 파트로 나눌 수 있다. 

- [코드를 리뷰하는 방법]({{ site.baseurl }}{% link review/reviewer.md %}): 코드 리뷰어를 위한 가이드
- [CL 작성자용 가이드]({{ site.baseurl }}{% link review/author.md %}): 리뷰를 받는 코드 작성자를 위한 가이드

### 적합한 리뷰어 고르기
당신의 코드를 빈틈없이 올바르게 리뷰해줄 수 있는 사람이 적합한 리뷰어다. 보통 원래 코드의 주인일 것이다. 때로는 CL의 각 부분을 다른 리뷰어에게 맡겨야 할수도 있다. 적합한 리뷰어를 찾았지만 리뷰를 해줄 수 없는 상황인 경우는 최소한 참조(CC)라도 해놓는다.

### 대면 리뷰
좋은 코드 리뷰를 해줄 자격이 있는 사람이랑 페어 프로그래밍을 했다면 해당 코드는 리뷰를 받은 것으로 간주한다. 대면 리뷰를 진행할 수도 있는데 이 경우에는 리뷰어가 질문을 하고 작성자는 질문을 받았을 때만 발언을 할 수 있다.
