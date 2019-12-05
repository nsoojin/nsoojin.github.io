---
layout: post
title: "스위프트의 콤마와 &&의 차이: condition과 expression의 구분"
categories: []
tags:
- swift comma
- condition-list
- AND operator
type: post
published: true
meta: {}
---

이번 주 인터넷을 돌아다니다가 [Swift의 if문에서 ‘,’와 ‘&&’의 차이](https://daeun28.github.io/호기심이_쏘아올린_작은공/Swift의_if문에서_','와_'&&'의_차이-post/)라는 재밌는 글을 발견했다. 재밌었던 이유는 명료한 답변이 곧바로 떠오르지 않았기 때문이다. 가끔 이렇게 평소에는 별 생각없이 당연하게 쓰던 것에 대한 질문에 말문이 막히는 경우가 있다.

결론부터 한줄 요약 하자면, `콤마는 condition을 이어붙이는 용도로 쓰는 것이고 &&는 두개의 boolean expression을 파라미터로 받는 논리 연산자이다.` 라고 결론 내릴 수 있겠다. 따라서 콤마와 &&의 차이는 *condition*과 *expression*의 차이를 이해하는데서 시작해야한다.

(1) 위에 "condition을 이어붙인다"고 했는데, 이렇게 이어붙여진 condition들을 condition-list라고 부른다. 스위프트 공식 문서에 따르면 [condition-list](https://docs.swift.org/swift-book/ReferenceManual/Statements.html#grammar_condition-list)는,

- `condition` 이거나
- `condition , condition-list` 이다.

정의에 재귀가 있다는 것만 유의하면 된다. 풀어서 말하면 condition-list는 condition 하나이거나 콤마로 condition을 (정의상 무한정?) 이어붙인 것이다.

- condition
- condition, condition
- condition, condition, condition
- condition, condition, condition, condition, ... 

(2) 그러면 condition은 뭘까? 마찬가지로 스위프트 공식 문서에 따르면 [condition](https://docs.swift.org/swift-book/ReferenceManual/Statements.html#grammar_condition)은 

- expression
- availability-condition
- case-condition
- optional-binding-condition 

이렇게 네 개 중에 하나를 나타낸다. 아래 세 개는 이름만으로도 뭘 의미하는 꽤 명확해보인다.

(3) 그럼 condition의 한 종류인 expression은 과연 뭘까? [expression](https://docs.swift.org/swift-book/ReferenceManual/Expressions.html#)도 위와 같이 계속 파고 들어갈 수 있지만 주제의 범위를 벗어나기 때문에 이쯤에서 그만하기로 하고, 최대한 간단하게 말해서 '연산자를 하나라도 쓴 스위프트 코드 단위'라고 볼 수 있다. 

(4) 결국 &&는 하나의 expression을 만들 수 있는 [수많은 연산자](https://developer.apple.com/documentation/swift/swift_standard_library/operator_declarations) 중에 하나인 것이고, &&를 쓴 expression은 (condition의 정의에 따라) condition이기도 한 것이다.

그렇다면 [원 질문의 예제](https://stackoverflow.com/questions/44989227/separating-multiple-if-conditions-with-commas-in-swift)로 돌아와서, 왜 첫번째는 되고 두번째는 안되는 것일까?

```swift
if let a = someOpt, let b = someOtherOpt { … } // works

if let a = someOpt && let b = someOtherOpt { … } // error
```

첫번째는 optional-binding-condition 두 개를 콤마로 붙인 condition-list 이기 때문에 잘 동작을 한다. 반면에 아래 코드는 && 논리 연산자를 썼는데, 논리 연산자는 두 개의 bool expression을 인자로 받는 연산자이다. 그런데 `let a = someOpt`와 `let b = someOtherOpt`는 bool expression이 아니다. 연산자를 잘못 썼기 때문에 당연히 에러가 난다. 비유하자면 `"hello" && "world"`가 안되는 것과 같은 이유이다.

또 다른 예제를 보게되면,

```swift
if 1 == 1, 2 == 2 { … } //works

if 1 == 1 && 2 == 2 { … } //works
```

둘다 기능적으로는 동일하게 동작하지만 컴파일러의 눈에는 조금 다르다.
첫번째는 condition(== 연산자를 쓴 expression)이 두 개이고, 두번째는 condition(&& 연산자를 쓴 expression)이 하나인 것이다.

결론으로 돌아오면, 콤마는 condition-list를 만드는 스위프트 문법의 종류이고, &&는 논리 연산자이다. 다시 말해 콤마 앞뒤에는 *condition*이 와야하고 && 앞뒤에는 *boolean expression*이 나와야 하는 것이다. 그리고 둘의 차이를 결정 짓는 핵심은 `expression은 condition이지만 condition은 expression이 아니다`라는 명제이다.

## 보너스

Q. condition과 condition-list를 굳이 구분해야하나?

A. 그렇다. 왜냐면 condition과 condition-list를 쓸 수 있는 곳이 다르다. while, if, guard 문에는 condition-list를 쓰지만 repeat-while 문에서는 condition만 쓸 수 있다. 콤마로 여러 condition을 이어붙이는 것이 허용된 곳과 아닌 곳이 있는 것이다.

