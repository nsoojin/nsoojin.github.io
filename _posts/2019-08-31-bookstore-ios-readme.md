---
layout: post
title: "BookStore iOS 샘플 앱"
categories: []
tags:
- ios
- swift
- unit testing
- ui testing
- network mocking
- frameworks
- optionals
type: post
published: true
meta: {}
---

아래 글은 [BookStore-iOS](https://github.com/nsoojin/BookStore-iOS) 프로젝트의 소개 글의 한글 버전입니다.

## 앱 개요

이 샘플 앱은 [IT Bookstore API](https://api.itbook.store)를 사용하여 최신 프로그래밍 책을 검색해볼 수 있는 앱이다.

해당 프로젝트는 `Result` 타입 활용, 유닛 테스팅, UI 테스팅, 네트워크 stubbing, 프레임워크 분리, 문서화 주석(documentation comment) 작성 등의 예제를 포함하고 있다.

## 내용

- `Result` 타입
- 유닛 테스트에서 네트워크 stubbing
- Mock 데이터로 UI 테스팅 하기
- 앱 로직을 프레임워크로 분리하기
- 문서화 주석 작성
- Implicitly Unwrapped Optional 제거하기

## `Result` 타입

보통은 `Result` 인스턴스에서 Success나 Failure 값에 접근하고 싶을때 스위치문을 쓰게 된다.

```swift
switch result {
case .success(let response):
  //response 객체 사용
case .failure(let error):
  //에러 처리
}
```

하지만 스위치문은 너무 문법이 길고 불필요한 case까지 써줘야하는 단점이 있다. 그래서 [success](https://github.com/nsoojin/BookStore/blob/5f3d7d85503fd1de51d04b0286653a3578a7125a/BookStore/Extensions/Result%20%2B%20Extensions.swift#L25)와 [catch](https://github.com/nsoojin/BookStore/blob/5f3d7d85503fd1de51d04b0286653a3578a7125a/BookStore/Extensions/Result%20%2B%20Extensions.swift#L34)라는 익스텐션 메서드를 추가해서 Result 인스턴스 값을 접근하는 데에 사용하면 매우 용이하다.

```swift
searchResult.success { response in
  //response 객체 사용
}.catch { error in
  //에러 처리
}
```

혹은

```swift
result.success(handleSuccess)
      .catch(handleError)
      
func handleSuccess(_ result: SearchResult) { ... }
func handleError(_ error: Error) { ... }
```

## 유닛 테스트에서 네트워킹 Stubbing

테스트에서 실제 네트워킹을 하는 것은 바람직한 방식이 아니다. 유닛 테스트에 지나치게 불확실한 의존성이 생기기 때문이다. URLSession을 사용하고 있다면 URLProtocol의 서브클래스를 만들어서 stubbing을 할 수 있다.

### 1. `URLProtocol` 서브클래스 생성

[MockURLProtocol](https://github.com/nsoojin/BookStore/blob/master/BookStoreKitTests/MockAPI/MockURLProtocol.swift) 참고

### 2. `MockURLProtocol`을 `URLSession`에 연결

```swift
let config = URLSessionConfiguration.ephemeral
config.protocolClasses = [MockURLProtocol.self]

//해당 session으로 네트워크 요청을 수행
let session = URLSession(configuration: config) 
```

### 4. 원래 쓰던 것처럼 `URLSession` 사용

```swift
session.dataTask(with: urlRequest) { (data, response, error) in
  //Mock 데이터가 전달됨
}.resume()
```

## Mock 데이터로 UI 테스팅 하기

유닛 테스트에서 사용하는 위 방식은 UI 테스팅에서는 작동하지 않는다. 테스트 번들과 XCUIApplication이 돌아가는 앱 번들이 서로 다른 프로세스에서 실행되기 때문이다. [Swifter](https://github.com/httpswift/swifter)라는 라이브러리를 쓰면 간단한 로컬 HTTP 서버를 시뮬레이터 내부에서 돌릴 수 있어서 앱 네트워킹을 localhost 서버로 보낼 수 있게 된다.

먼저 `launchArguments`로 값을 전달하여 UI 테스팅 중에만 host를 바꿔준다.

```swift
//XCTestCase 내부
override func setUp() {
  app = XCUIApplication()
  app.launchArguments = ["-uitesting"]
}

//AppDelegate의 application(_:didFinishLaunchingWithOptions:) 메서드 내부
if ProcessInfo.processInfo.arguments.contains("-uitesting") {
  BookStoreConfiguration.shared.setBaseURL(URL(string: "http://localhost:8080")!)
}
```

아래처럼 Swifter 서버 인스턴스에 mock 데이터를 세팅해준다.

```swift
let server = HttpServer()

func testNewBooksNormal() {
  do {
    let path = try TestUtil.path(for: normalResponseJSONFilename, in: type(of: self))
    server[newBooksPath] = shareFile(path)
    try server.start()
    app.launch()
  } catch {
    XCTAssert(false, "Swifter Server failed to start.")
  }
        
  XCTContext.runActivity(named: "Test Successful TableView Screen") { _ in
    XCTAssert(app.tables[tableViewIdentifier].waitForExistence(timeout: 3))
    XCTAssert(app.tables[tableViewIdentifier].cells.count > 0)
    XCTAssert(app.staticTexts["9781788476249"].exists)
    XCTAssert(app.staticTexts["$44.99"].exists)
  }
}
```

## 앱 로직을 프레임워크로 분리

앱의 기능 로직을 별도 타겟 프레임워크로 분리하면 여러 장점이 있다. 유닛 테스트의 기준이 명확히 분리되고 디펜던시에 대해 고려할 수 밖에 없게 된다. 반면에 프레임워크 로딩 때문에 앱 실행이 조금 느려질 수 있다.

`BookStoreKit`은 [IT Bookstore API](https://api.itbook.store)와 통신하여 도서 정보와 검색하는 기능을 책임지는 프레임워크이다. `Networking`은 URLSession을 기반으로 HTTP 통신과 응답 데이터 파싱을 간편하게 해주는 프레임워크이다.

<img src="https://raw.githubusercontent.com/nsoojin/BookStore/master/README_assets/xcodeproj-targets.png" width="200">

## 문서화 주식 작성하기

[스위프트 API 디자인 가이드라인](https://swift.org/documentation/api-design-guidelines/#fundamentals)에서는 모든 선언문에 문서화 주석을 달기를 추천하고 있고, 이해하기 쉬운 설명을 다는 행위만으로도 설계에 좋은 영향을 준다고 한다.

### 1. 열심히 작성

이 [문서](https://developer.apple.com/library/archive/documentation/Xcode/Reference/xcode_markup_formatting_ref/index.html)에서 마크업 문법을 참고하여 작성한다.

예)
<img src="https://raw.githubusercontent.com/nsoojin/BookStore/master/README_assets/documentation-0.png">

### 2. 결과물을 확인/활용 한다.

Xcode 자동완성 기능

<img src="https://raw.githubusercontent.com/nsoojin/BookStore/master/README_assets/documentation-1.png" width="400">

도움말 보기 기능 (option + click)

<img src="https://raw.githubusercontent.com/nsoojin/BookStore/master/README_assets/documentation-2.png" width="600">

## Implicitly Unwrapped Optional 제거하기

스위프트에서 `!`는 경고 표시이다. 크래시가 발생할 수 있는 가능성을 내포하는 코드이기 때문에 최대한 쓰지 않는 것이 좋다. 아래 두가지 방식으로 iOS 개발에서 종종 사용되는 IUO를 줄일 수 있다.

### IBOutlet을 옵셔널로 바꾸기

스토리보드에서 뷰컨트롤러로 IBOutlet을 연결하면 기본적으로 IUO로 생성된다. 하지만 아울렛이 반드시 IUO일 이유는 전혀 없다. 보통 아울렛으로 연결된 뷰들은 값을 넣는 작업이 많기 때문에 옵셔널로 바꾸더라도 if let이나 guard가 생기지 않는다. 

<img src="https://raw.githubusercontent.com/nsoojin/BookStore/master/README_assets/optional-iboutlets.png" width="500">

### lazy 초기화 활용

UIViewController에 프로퍼티를 추가할때 종종 IUO를 사용한다. 뷰컨트롤러의 생성자를 오버라이딩해서 값을 넣어줄 수 없기 때문인데 이 경우에도 약간의 코드로 IUO를 없애줄 수 있다. [unspecified](https://github.com/nsoojin/BookStore/blob/e27ea7252189e9f7ed2b7a9494334ccab9ce801c/BookStore/Extensions/Unspecified.swift#L11)라는 함수를 하나 만들어서 아래처럼 lazy var로 선언을 해주면 된다. 이렇게 하면 위험한 IUO나 언래핑하기 번거로운 옵셔널을 쓰지 않아도 된다.

```swift
lazy var bookStore: BookStoreService = unspecified()
```
