---
layout: post
title: "스위프트 API 디자인 가이드라인 초초초-요약본"
categories: []
tags:
- Swift API Design Guidelines
- abriged
type: post
published: true
meta: {}
---

[https://swift.org](https://swift.org/documentation/#api-design-guidelines) 에서 스위프트 API 디자인 가이드라인의 존재 이유를 다음과 같이 설명하고 있다.

> 스위프트 3.0 출시의 목표는 프로그래머에게 일관된 사용자 경험을 제공하기 위한 표준을 세우는 것이고, API에 등장하는 이름과 표현법을 통해 이를 정의하고 있다. 이 가이드라인은 여러분의 코드가 더 큰 스위프트 생태계의 일부인 것처럼 느껴지게 하는 방법을 설명하고 있다.

‘프로그래머의 사용자 경험’이라는 단어를 썼다는 것이 어색하기도 하고 놀랍기도 하고 한편으론 정말 애플스럽다. 그런데 왠지 익숙한 표현법 아닌가? 

> 애플 플랫폼과 매끄럽게 어우러지는 훌륭한 앱을 디자인 하기 위한 ... (후략)

> 잘 알려진 아이콘, 텍스트 스타일, 통일성 있는 용어, 시스템이 제공하는 인터페이스 요소들을 활용하여 유저에게 일관된 경험을 제공 ... (후략)

[Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/)에 등장하는 문장들이다. 최종 사용자들에게 좋은 사용자경험을 주는 애플 플랫폼 앱을 만들기 위해 따라야하는 것이 ‘휴먼 인터페이스 가이드라인’이라면, 스위프트 개발자에게 좋은 사용자경험을 주는 코드를 만들기 위해 따라야하는 것은 ‘스위프트 API 디자인 가이드라인’인 셈이다.

가이드라인에는 꽤 많은 내용이 담겨 있다. 그렇다고 문서가 눈에 쏙쏙 들어오는 편도 아니고 누구나 들었을때 ‘음 그렇지’라고 고개가 끄덕여지는 그런 내용도 아니다. 이런 이유로 가이드라인에 무슨 내용이 담겨 있는지, 어디서부터 시작해야될지 잘 모르겠는 분들이 많은 것 같다. 약 일 년 정도 API 디자인 가이드라인과 애플 개발자 문서를 옆구리에 끼고, 아니 모니터에 띄워놓고 개발했던 경험을 토대로 내 마음속 중요도 순으로 추리고 요약해봤다. 그룹핑도 적용 상황을 기준으로 내 마음대로 재구성했다.

⚠️ 가이드라인의 모든 내용을 담고 있지 않다. 한 20% 정도? 하지만 적용되는 빈도로 따지면 아래 내용들을 합치면 전체 케이스의 50%는 넘는 것 같다.

## 함수 및 프로퍼티 이름

- **메서드나 함수는 사이드 이펙트의 유무에 따라 이름 짓는다.**

	- 사이드 이펙트 없는 함수는 `명사구`로 읽혀야한다.<br><br>
	
	⛔️ 잘못된 예
	```swift
	x.getDistance(to: y) //"y까지의 거리를 가져와라"
	```

	✅ 올바른 예
	```swift
	x.distance(to: y) //"y까지의 거리"
	```

	사이드 이펙트 없이 값을 리턴하는 메서드(일명 "getter")에 get을 붙이는 것이 대표적인 가이드라인 위배이다. 코코아터치 프레임워크에서 `get`, `fetch`, `request`로 시작하는 메서드는 전부 completion handler를 받는 비동기 작업 뿐이다.

	참고 : [Swift 개발자처럼 변수 이름 짓기](https://soojin.ro/blog/english-for-developers-swift)

	- 사이드 이펙트가 있는 함수는 `명령형 동사구`로 읽혀야한다. (명령형이라는 것은 동사원형을 쓴다는 것이다.)

	```swift
	print(x)
	x.sort()
	x.append(y)
	```

	위 함수들은 모두 함수 스코프 밖에까지 영향을 미친다(사이드 이펙트). print는 콘솔에 찍히고 sort와 append는 x의 값을 바꿔버린다.

- **mutating과 nonmutating 메서드 쌍은 일관성있게 이름 짓는다.** 보통 mutating 함수는 명령형 동사로 쓰고 nonmutating은 "ed"나 "ing"를 뒤에 붙여서 사용한다.

|Mutating|Nonmutating|
|-------|-------|
|x.sort()|z = x.sorted()|
|x.append(y)|z = x.appending(y)|

- **Bool 메서드나 프로퍼티 이름은 인스턴스에 대한 평서문처럼 읽혀야한다.**

	e.g. `x.isEmpty`, `line1.intersects(line2)`

	참고 : [Bool 변수 이름 제대로 짓기 위한 최소한의 영어 문법](https://soojin.ro/blog/naming-boolean-variables)

- **대소문자 규칙을 따른다.**
	- 타입이나 프로토콜은 UpperCamelCase를 따르고 그 외에는 lowerCamelCase를 따른다.
	- 대문자 약어는 낙타표기법에 따라 <U>전체 대문자 혹은 소문자로 통일한다</U>.

	```swift
	var utf8Bytes: [UTF8.CodeUnit]
	var isRepresentableAsASCII = true
	var userSMTPServer: SecureSMTPServer
	```

	우리가 많이 쓰는 대문자 약어 중에 URL이 이 조건에 해당될 것이다. 규칙을 따르려면 아래처럼 사용해야 한다.

	```swift
	let urlString = "https://soojin.ro"
	let blogURL = URL(string: urlString)
	```

## 파라미터명(Parameter Names)

파라미터 명명 규칙은 좀 더 세부적으로 `생성자`일 때와 `메서드`일 때로 나뉜다.

### 생성자

- **생성자의 첫번째 파라미터명은 타입 이름과 구(phrase)를 이뤄서는 안된다.**
	다시 말해 생성자의 파라미터명에는 아래처럼 전치사나 접속사 등을 써서 문장처럼 이어지게 만들지 말라는 말이다.

	⛔️ 잘못된 예
	```swift
	let foreground = Color(havingRGBValuesRed: 32, green: 64, andBlue: 128)
	let newPart = factory.makeWidget(havingGearCount: 42, andSpindleCount: 14)
	let ref = Link(to: destination)
	```

	대신 아래처럼 having, and, to, with 등의 단어들을 제거한다.

	✅ 올바른 예
	```swift
	let foreground = Color(red: 32, green: 64, blue: 128)
	let newPart = factory.makeWidget(gears: 42, spindles: 14)
	let ref = Link(target: destination)
	```

- **무손실 타입 변환(value preserving type conversion)을 하는 생성자는 첫번째 파라미터명을 생략한다.** 추가적으로, 손실이 일어나는 타입 변환이라면 어떤 손실이 일어나는지 명시해주는 것을 추천한다.

	✅ 올바른 예
	```swift
	Int64(someUInt32)
	String(someNumber)
	String(someNumber, radix: 16)
	UInt32(truncating: someUInt64) //64비트 -> 32비트로 손실 발생 명시
	```

<U>즉, 무손실 타입 변환이 일어나는 생성자가 아니라면 첫번째 파라미터에는 이름을 꼭 부여한다.</U>

### 메서드 및 함수

- **첫번째 파라미터가 전치사(to, in, at, from, with 등등)구의 일부라면 파라미터명을 부여한다.**

	✅ 올바른 예
	```swift
	x.removeBoxes(havingLength: 12)
	employees.remove(at: x)
	```

- **그렇지 않고 첫번째 파라미터가 자연스럽게 이어지면 생략한다.**

	✅ 올바른 예
	```swift
	x.addSubview(y)
	allViews.remove(cancelButton)
	x.insert(y, at: z)
	x.append(y)
	```

- **그 외 모든 경우에는 파라미터 이름을 부여한다.**

끝.

[가이드라인 원본](https://swift.org/documentation/api-design-guidelines/)
[가이드라인 번역본](https://minsone.github.io/swift-internals/api-design-guidelines/?utm_source=soojinro&utm_medium=referral)


