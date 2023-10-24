---
layout: post
title: "[기본기] Swift의 변수, 상수 이해하기"
categories: []
tags:
- swift
- mutation
- variable
- constants
type: post
published: true
meta: {}
---

## 변수와 상수의 차이가 뭐에요?

Swift에서 변수를 선언할 때는 var, 상수를 선언할 때는 let 키워드를 사용한다. 보통 변수는 `값을 바꿀 수 있는 것`, 상수는 `값을 바꿀 수 없는 것`이라고 말한다. 변수가 기본 자료형일 때는 값을 바꾼다 라는게 무슨 뜻인지 매우 명확하다. 정수 1을 2로 바꾼다, 소수 3.14를 3.15로 바꾼다, true를 false로 바꾼다 등. 반면에 변수가 객체일 때는 생각할게 좀 더 많아진다.

변수가 객체일 때 "값을 바꾼다(mutation)"는 두 가지 의미가 있을 수 있다. 객체의 속성을 바꾸는 것도 될 수 있고, 변수가 가르키는 인스턴스를 바꾸는 것도 될 수 있다.
```swift
// 1. 객체의 속성을 바꾼다.
var person = Person(name: "John")
person.name = "Jane"

// 2. 변수가 가르키는 인스턴스를 바꾼다.
var person = Person(name: "John")
person = Person(name: "Jane")
```

그렇다면 값을 바꾼다는건 이 중에 어떤걸 의미하는걸까? 객체를 var로 선언하면 둘 다 될까? let으로 선언하면 둘 다 안될까? 여기에 객체의 종류까지 추가해서 Person 객체가 struct일 때와 class일 때를 각각 따져본다.

정리하자면 다음처럼 여덟 가지 경우의 수가 나온다.

| 객체 종류 | 선언 방식 | 객체의 속성을 바꿀 수 있는가? | 변수가 가르키는 인스턴스를 바꿀 수 있는가? |
| --- | --- | --- | --- |
| struct | var | ? | ? |
| class | var | ? | ? |
| struct | let | ? | ? |
| class | let | ? | ? |

이를 토대로 던져볼 수 있는 질문들
- var와 let을 나누는 기준은 무엇인가?
- struct와 class가 mutation을 나누는 기준은 무엇인가?
- 값 타입과 참조 타입이라는 것이 영향을 미치는가?
- 객체의 속성을 바꿀때 class와 struct에서는 각각 어떤 일이 일어나는가?
- class와 struct 중에 선택할 때 고려해야 하는 것들은?

등등...