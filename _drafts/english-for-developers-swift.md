---
layout: post
title: "개발자를 위한 영어 for Swift"
categories: []
tags:
- english grammar
- swift
status: publish
type: post
published: true
meta: {}
---

1979년에 발간됐지만 여전히 프로그래밍 입문서로 유명한 *Structures and Interpretation of Computer Programs*라는 책의 도입부에 이런 말이 있다.
>Programs should be written for people to read, and only incidentally for machines to execute.

>(내맘대로 의역) 프로그램은 사람들에게 읽히기 위한 목적으로 만들어져야 하고, 우연히 컴퓨터가 실행할 수 있다면 더욱 좋다.

신입 개발자로 입사했던 첫 해에 배운 것 중 하나를 꼽으라 한다면 코드를 쓰는 시간보다 코드를 읽는 시간이 더 많다는 것이다. 협업을 해보니 사용자의 입장 뿐 아니라 독자의 입장도 고려해야 한다고 느끼게 됐다. 앱스토어에 올리는 앱만 결과물이 아니라 코드도 결과물이라는 것? 그래서 UI/UX를 만들때는 유저에게 감정 이입을 하고, 코드로 로직을 구현할때는 나중에 이 코드를 읽을 사람(나 포함)에게 감정 이입을 하는 것이 프로그래머의 과업인 것 같다.

## 영어와 Naming Conventions

영어가 외국어인 우리는 컨벤션도 지켜야하고 영어도 고민해야하는 이중고에 처해 있다고 생각할수도 있지만 딱히 억울해 할 것도 없다. 영어가 모국어인 개발자가 전세계에 얼마나 될까. 미국인은 전세계 인구의 5%도 안된다. 지난번 글 [Bool 변수 이름 제대로 짓기 위한 최소한의 영어 문법](https://soojin.ro/blog/naming-boolean-variables)처럼, 알아두면 유용한 영어 문법과 실수하기 쉬운 스위프트 코딩 컨벤션 몇가지를 소개한다.

### #1 동사의 변형

영어에서 동사는 세 가지 형태로 사용된다. ***동사원형 - 과거형 - 과거분사***


### Chaining functions w/o side effects

[2016년 D2 iOS 오픈세미나](http://d2.naver.com/news/9814448)에서 [발표](https://www.slideshare.net/soojinro9/d2-63435589)했던 걸 계기로 파파고와 웨일 브라우저를 개발한 팀의 리더님을 알게되어 그 팀에서 단기로 프로젝트를 했던 적이 있다. 멘토님에게서 배운 여러가지 중 가장 기억에 남고 지금도 코딩하면서 매일 실천하기 위해 노력하는 것은 **함수는 10줄을 넘기지 않는다**이다. 자극적으로 들릴 수도 있지만 본질적으로 함수는 하나의 작업만 하도록 짜라는 강령으로 이해했다. 10줄 이내로 짜려는 노력을 하다보면 계속해서 함수는 작은 기능 단위로 쪼개지고 나는 객체 간의 관계를 설계하는데 고민하는 시간이 늘게 된다. 더 나아가 side effect가 없는 함수들을 체이닝 하면 코드가 쉽게 읽히고 수정이 용이해진다는 장점이 있다. 특히 스위프트의 `map`, `flatMap`, `compactMap`, `filter` 등의 메서드와 함께 쓸 때 빛을 발한다. Promises에서는 `then`, `always`, `validate` 등으로 함수를 체이닝할 수 있다.

예시:
```swift
extension CNContact {
  var mainPhoneNumber: String? {
    return phoneNumbers.map { $0.value.stringValue }
                        .filter(prefixValidater)
                        .map(replaceKoreanCountryCode)
                        .map(removeNonNumerics)
                        .filter { $0.isPhoneNumber && $0.count == 11}
                        .first
  }

  private func prefixValidater(_ target: String) -> Bool { ... }
  private func replaceKoreanCountryCode(_ digits: String) -> String { ... }
  private func removeNonNumerics(_ digits: String) -> String { ... }
}
```

### Conveying meaningful error messages to users

모바일 앱 개발을 몇 년 하다보니 어떻게 에러 핸들링을 잘 할 수 있을까 하는 갈증이 생겼다. 모바일 앱에서 가장 많이 일어나는 작업의 단계는 `유저 인터랙션` 👉 `요청을 처리하기 위한 일련의 작업` 👉 `화면에 결과 보여주기` 인데 일련의 작업을 처리하다보면 다양한 에러가 발생할 수 있다. 데이터가 변질됐거나, 네트워크가 불안정하거나, 서버가 다운됐거나, 권한이 없다거나 하는 등. 이때 단순히 "요청이 실패했습니다"라는 의미없는 메시지보다는 에러의 원인을 유저에게 알리는 것이 사용자 경험 측면에서 월등히 좋다. `NSError`의 localizedDescription을 활용하거나 스위프트에서는 `Error` 혹은 `LocalizedError` 프로토콜을 사용할 수 있다. Promises에서는 여러 연속된 비동기 작업 도중 발생한 에러를 최종 단계에서 통일성 있게 전달 받을 수 있고, 심지어 `recover`로 복구할 기회도 있다.

이런 에러 메시지를 보여줄 것인가?

<img src="/assets/posts/unhelpful-error-message.jpeg" />

아니면 실질적으로 유저에게 도움이 되는 메시지를 보여줄 것인가?

<img src="/assets/posts/meaningful-error-message.jpeg" />

### Minimizing dependencies

스탠다드 라이브러리를 감싼 무슨무슨 ~Kit을 쓰는 것에 대한 미묘한 거부감이 있다. 아이폰도 케이스 없이 보호 필름만 붙이고 다니는 성격이라 그런지 프로젝트 한 두군데에서만 쓰기 위해, 혹은 필요한 1만큼의 기능을 가져다 쓰기 위해 10 크기의 라이브러리를 코코아팟에 이것저것 추가하는 것이 무척 꺼려진다. 오토레이아웃도 별도 라이브러리 없이 쓰고 있고 한때 관심을 가졌던 Rx도 공부하다가 결국 과도한 튜닝을 하는 것 같아 도외시했다. 튜닝의 끝은 순정이라고, 순정을 선호하는 입장에서 Promises는 다른 유사 라이브러리보다 [GCD](https://developer.apple.com/documentation/dispatch)를 더 가볍게 감쌌기 때문에 성능에서 앞서고 학습 비용도 적은 것 같다.

## Adopting Promises to Your Project

Promises를 도입하겠다고 해서 당장 프로젝트 전체를 뜯어고치지 않아도 되기 때문에 실무에서 차근차근 적용해보기에도 좋다. 보통 작업의 특성별로 담당 클래스가 있을텐데(e.g. 로그인매니저, 이미지다운로더 클래스 등과 같은) 이런 클래스 한 두개만 우선적으로 적용하며 맛보기를 해봐도 충분하다. 또한 [`wrap`](https://github.com/google/promises/blob/master/g3doc/index.md#wrap)을 쓰면 기존에 있던 코드를 손댈 필요도 없이 코드 몇 줄로 Promises식 비동기 함수를 만들 수도 있다.

기존에 completion handler를 파라미터로 받던 함수:
```swift
func data(from url: URL, completion: @escaping (Data?, Error?) -> Void)
```

Promise 객체를 리턴하도록 수정한 비동기 함수: 
```swift
func data(from url: URL) -> Promise<Data>
```

그러면 함수 내부에서 [Promise 객체를 생성](https://www.github.com/google/promises/blob/master/g3doc/index.md#creating-promises)하여 리턴해준 후, [fulfill](https://github.com/google/promises/blob/master/g3doc/index.md#then)된/될 결과값을 가지고 추가적인 작업을 해주면 된다.

```swift
let url = ...
data(from: url).then { data in
  //data로 추가 작업
}.catch { error in
  //error 처리
}
```

더 다채로운 활용법은 [문서](https://github.com/google/promises/blob/master/README.md)에 간단명료하게 잘 설명되어 있다!

## Use Cases

그동안 사용해보면서 시행착오를 거쳐 습득한 몇 가지 유즈케이스 및 주의사항을 정리했다.

### Partial Application 기법

Promises 파이프라인의 가독성과 함수 재활용성을 높이기 위해 [partial application](http://paul-samuels.com/blog/2018/01/31/swift-partially-applied-functions/) 기법을 활용하는 방법을 소개한다. `map`, `forEach` 등의 higher order function들을 사용하고 있었다면 아마 써본적이 있을 확률이 높다.

API 서버에 로그인하여 access token을 받아오는 작업은 로그인 기반의 서비스에서 빠질 수 없는 작업이다. 가상의 로그인 단계는 다음과 같다. `회원가입` 👉 `로그인` 👉 `엑세스 토큰 획득`. 하지만 이미 회원가입이 되어 있는 유저라면 회원가입에 실패하게 된다. Promises에서는 실패했을때 `recover`를 사용해서 실패를 복구할 기회가 있다. 그래서 만약 회원가입 실패의 원인이 duplicate user라면 로그인을 시도한다.

Promises 코드:
```swift
typealias MyAccessToken = String

func retrieveAccessToken(with naverToken: String) -> Promise<MyAccessToken> {
  return requestSignUp(with: naverToken)
         .then(signIn(with: naverToken))
         .recover(onError(with: naverToken))
}

//Async Server API calls
func requestSignUp(with naverToken: String) -> Promise<SignUpResponse> { ... }
func requestSignIn(with naverToken: String) -> Promise<MyAccessToken> { ... }

//partially applied functions
func signIn(with naverToken: String) -> (SignUpResponse) -> Promise<MyAccessToken> {
  return { _ in requestSignIn(with: naverToken) }
}

func onError(with naverToken:String) -> (Error) -> Promise<MyAccessToken> {
  return { error in
    switch error {
    case SignUpError.duplicateUser:
      return requestSignIn(with: naverToken)
    default:
      return Promise(error)
    }
  }
}
```

`signIn(with:)`과 `onError(with:)`는 각각 SignUpResponse와 Error 파라미터를 나중에 전달 받도록 짠 partially applied functions이다. 이런 식으로 체이닝을 할 때 클로져를 바로 쓰지 않고 partial application을 활용하여 기존의 API 관련 함수들을 재활용함과 동시에 비동기 작업 파이프라인을 훨씬 읽기 쉽게 만들었다.


### 단순 체이닝으로 불가능한 작업은 `await`으로

`await`은 여러 비동기 작업으로부터 얻은 결과들을 혼합해서 사용해야 할때 유용하다. 

예를 들어, 썸네일 이미지에 대한 url을 가지고 UIImage와 해당 이미지의 대표 UIColor 추출해 화면에 그려야 하는 작업이 있다고 가정해본다. 정리하면 `URL을 UIImage로 변환` 👉 `UIImage에서 UIColor 추출` 👉 `UIImage, UIColor를 가지고 화면에 썸네일 생성` 해야했는데 이 작업은 `then` 체이닝만으로는 구현하기 어려웠고 이를 `await`으로 해결했다. 또한 이 방식은 비동기 작업을 동기적인 코드처럼 쓰고 싶을 때 사용해도 된다.

Promises 코드:
```swift
typealias ThumbnailData = (image: UIImage, color: UIColor)

func thumbnailData(from url: URL) -> Promise<ThumbnailData> {
  return Promise<ThumbnailData>(on: queue) { //queue는 백그라운드 DispatchQueue
    let image = try await(image(from: url))
    let color = try await(dominantColor(from: image))

    return (image: image, color: color)
  }
}

//Async functions
func image(from url: URL) -> Promise<UIImage> { ... }
func dominantColor(from image: UIImage) -> Promise<UIColor> { ... }

```

활용한 부분:
```swift
let cell: MyTableViewCell = ...
let myDatum = data[indexPath.row]
let url: URL = myDatum.imageURL

thumbnailData(from: url).then { result in
  cell.imageView.image = result.image
  cell.dominantColorView.backgroundColor = result.color
}

```

추가적으로 Promises 사용 전 꼭 알아두면 좋은 내용으로는,
- [안티 패턴](https://github.com/google/promises/blob/master/g3doc/index.md#anti-patterns) 피하기
- [retain cycle](https://github.com/google/promises/blob/master/g3doc/index.md#ownership-and-retain-cycles)에 대해 염두하기
- 여러가지 유용한 [extensions](https://github.com/google/promises/blob/master/g3doc/index.md#extensions)

정도가 있다.

## Wrap Up

iOS 개발을 하면서 한번이라도 completion handler 방식의 비동기 프로그래밍에 아쉬움을 느껴봤거나 Rx는 나의 필요 이상으로 너무 방대하다는 생각이 든 적이 있다면 한번 시도해보길 추천한다. [google/promises](https://github.com/google/promises)는 최소한의 코드 변형으로 비동기 코드를 유연하게 하고, 가독성을 높이고, 적은 학습 비용으로 여러가지 시도해볼 수 있는 확장성 있는 좋은 프레임워크인 것 같다. 



