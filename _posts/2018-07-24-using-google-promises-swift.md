---
layout: post
title: "google/promises를 활용한 스위프트 비동기 프로그래밍과 에러 핸들링"
categories: []
tags:
- swift
- promises
- async programming
- error handling
status: publish
type: post
published: true
meta: {}
---

## Background

올해 1~2월 즈음 우연히 [google/promises](https://github.com/google/promises)를 알게 되었는데 소개 글과 샘플 코드 몇 줄을 보고 나서 ‘이건 꼭 써봐야겠다’는 생각이 들었다. 뭘 하는 프레임워크인지는 깃헙에 잘 소개되어 있어 상세한 설명은 생략하지만 한 줄 요약을 하자면 비동기 작업에 대한 결과를 completion handler로 처리하는 iOS의 특성에 기인하는 nested closures 문제를 해소할 수 있다. 여기에 덤으로 에러 처리까지 깔끔해진다.

Promises를 보자마자 써봐야겠다고 느꼈던 이유는 그 전부터 내 코드에서 시도하고자 했던 것들이 있었는데 이 라이브러리가 그걸 달성할 수 있게 해줄것 같았기 때문이다.
- 사이드 이펙트 없는 함수들의 체이닝
- 유저에게 의미있는 에러 핸들링
- 최소한의 튜닝(디펜던시)

이런 작은 목표들은 **작업의 흐름을 읽기 쉬운 코드**를 짜고 **유저에게 더 나은 경험**을 주고자 하는 최종적인 목표에 도움이 될 것이라 생각했다.

### Chaining functions w/o side effects

[2016년 D2 iOS 오픈세미나](http://d2.naver.com/news/9814448)에서 [발표](https://www.slideshare.net/soojinro9/d2-63435589)했던 걸 계기로 파파고와 웨일 브라우저를 개발한 팀의 리더님을 알게되어 그 팀에서 단기로 프로젝트를 했던 적이 있다. 멘토님에게서 배운 여러가지 중 가장 기억에 남고 지금도 코딩하면서 매일 실천하기 위해 노력하는 것은 **함수는 10줄을 넘기지 않는다**이다. 자극적으로 들릴 수도 있지만 본질적으로 함수는 하나의 작업만 하도록 짜라는 강령으로 이해했다. 10줄 이내로 짜려는 노력을 하다보면 계속해서 함수는 작은 기능 단위로 쪼개지고 나는 객체 간의 관계를 설계하는데 고민하는 시간이 늘었다. 더 나아가 side effect가 없는 함수들을 체이닝 하면 코드가 쉽게 읽히고 수정이 용이해진다는 장점이 있다. 특히 스위프트의 `map`, `flatMap`, `compactMap`, `filter` 등의 메서드와 함께 쓸 때 빛을 발한다. Promises에서는 `then`, `always`, `validate` 등으로 함수를 체이닝할 수 있다.

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

![Unhelpful Error Message](/assets/posts/unhelpful-error-message.png)

아니면 실질적으로 유저에게 도움이 되는 메시지를 보여줄 것인가?

![Meaningful Error Message](/assets/posts/meaningful-error-message.png)

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

### 함수형 프로그래밍의 currying 기법

Promises 파이프라인의 가독성과 함수 재활용성을 높이기 위해 [currying](https://robots.thoughtbot.com/introduction-to-function-currying-in-swift) 기법을 활용하는 방법을 소개한다. 

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

//curried functions
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

`signIn(with:)`과 `onError(with:)`는 각각 SignUpResponse와 Error 파라미터를 **나중에 전달 받도록** [currying](https://robots.thoughtbot.com/introduction-to-function-currying-in-swift)된 함수이다. 이런 식으로 체이닝을 할 때 클로져를 바로 쓰지 않고 함수를 currying하여 기존의 API 관련 함수들을 재활용함과 동시에 비동기 작업 파이프라인을 훨씬 읽기 쉽게 만들었다.


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

iOS 개발을 하면서 한번이라도 completion handler 방식의 비동기 프로그래밍에 아쉬움이나 답답함을 느껴본적 있다면 한번 시도해보길 추천한다. [google/promises](https://github.com/google/promises)는 최소한의 코드 변형으로 나의 비동기 코드가 유연해지고 가독성이 높아지고, 적은 학습 비용으로 여러가지 시도해볼 수 있는 확장성 있는 좋은 프레임워크인 것 같다. 



