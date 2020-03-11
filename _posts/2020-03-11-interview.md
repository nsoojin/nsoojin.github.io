---
layout: post
title: "괜찮은 iOS 면접 질문"
categories: []
tags:
- ios
- tech interview
type: post
published: true
meta: {}
---

면접 중에 받았던 iOS 관련 질문 중 괜찮다고 생각했던 것들을 모아봤습니다. 난이도 순으로 정렬했습니다.

- class와 struct의 차이
- class A와 class B에 동일한 함수가 있다면 어떻게 바꿀 수 있나?
- enum / function / closure 각각 value type 인가 reference type 인가?
- Array, Dictionary, Set 등등의 컬렉션 타입은 value type 인가 reference type 인가?
- 컬렉션 타입에는 수 십만, 수 백만 개의 element가 들어있을수 있는데 스위프트는 이를 어떻게 최적화 하는가?
- Optional은 스위프트에서 어떻게 구현되어 있는가?
- inter-object communication에 사용하는 디자인 패턴을 아는대로 말해보아라. (최소 3개)
- heap와 stack의 차이는 무엇인가? (heap와 stack에 저장되는 스위프트 객체의 다른점이 무엇인가? 나누는 기준이 무엇인가?)
- <details><summary>위에 질문 팔로업 (스포 포함)</summary>
    value type이 heap에 저장되는 경우는 언제인가?
  </details>
- retain cycle은 언제 / 어떻게 발생하는가?
- ARC에서 retain count는 무엇인가? compile time 기능인가 runtime 기능인가? 
- `class B: A` 일 때 둘다 `func one()`이라는 함수가 있으면 B 인스턴스나 A 인스턴스에서 호출했을때 어떤 함수를 호출할지 어떻게 판단하는가? 
