---
layout: post
title: "느슨하게 연결된 코드를 작성하고 계신가요?"
categories: []
tags:
- loosely coupled code
- modularization
type: post
published: true
meta: {}
---

모듈화의 첫 단추는 코드의 계층을 잘 정의하는 것입니다. UI 계층은 도메인 계층에만 의존하게 하고, 데이터 접근 계층에는 의존하지 않아야 합니다. 또한 도메인 계층과 데이터 접근 계층의 의존성을 역전시키는 것이 매우 중요합니다. 그렇게 하면 앱을 데이터 접근 계층으로부터 독립시켜서, 앱을 composable하게 만들 수 있고 앱 전체를 수정하지 않고도 데이터 접근 계층을 교체하거나 수정할 수 있습니다.

<img src="/assets/posts/loosely-coupled-code.png" />
