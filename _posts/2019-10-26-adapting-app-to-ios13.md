---
layout: post
title: "개인 앱 iOS 13 대응 후기"
categories: []
tags:
- iOS 13
- dark mode
- propertyWrapper
- SF symbols
type: post
published: true
meta: {}
---

## Background

iOS 13에 다크 모드가 추가돼서 개인 앱 [보안카드 위젯](https://apps.apple.com/kr/app/보안카드-위젯/id949362849)을 오랜만에 업데이트했다. 이왕 하는 김에 9000줄 정도의 옵젝씨 코드도 전부 스위프트 전환을 했고 기존에 쓰던 C++로 된 SMTP 라이브러리도 더 가벼운 스위프트 라이브러리로 바꿨다. Pod도 전부 제거하고 이제는 카르타고로 파이어베이스만 외부에서 가져와 빌드를 하고 있다. 앱 용량도 15메가에서 6메가 정도로 줄어서 기분이 좋다. 오래된 집을 깔끔하게 리모델링한 기분이다. 

## 1️⃣ .pageSheet 모달

개발자 입장에서 가장 충격적인 변화는 modal presentation의 기본 값이 바뀌었다는 점이다. 기존에는 [fullScreen](https://developer.apple.com/documentation/uikit/uimodalpresentationstyle/fullscreen)이 기본이었고 화면 전체를 가렸다. iOS 13에서는 [pageSheet](https://developer.apple.com/documentation/uikit/uimodalpresentationstyle/pagesheet)가 기본이 되었고 이 프레젠테이션은 화면 전체를 가리지 않고 상단에 살짝 걸쳐 있다. 그리고 화면을 드래그해서 뷰를 내릴 수 있다. iOS 13이 나오기 전부터 페이스북 앱에서는 외부 링크용 웹뷰가 이런식으로 되어있었고 지도나 내비게이션 앱, AR 앱 등에서 최근 급격히 많이 보이기 시작했다. 이와 관련된 오픈소스도 굉장히 많이 등장했다.

아이폰의 크기가 많이 커진 상황에서 기존의 모달을 닫으려면 스크린 맨 위에 있는 버튼에 닿기 위해 위태롭게 그립을 바꾸거나 다른 손을 써야 했었다. 하지만 손가락을 멀리 뻗지 않고도 드래그 제스처로 뷰를 내릴 수 있다는 점에서 이런 형태의 모달이 개인적으로 매우 유용했고 앞으로 아이폰 앱에서 쓸 일이 많을 것이라고 생각했었다. 그래서 네이티브로 이런 UI를 사용할 수 있게 돼서 기쁜 한편, 이렇게 하위 호환성을 깨뜨리는 방식으로 급격히 변화를 가져 올 줄은 몰랐다. [Modernizing Your UI for iOS 13](https://developer.apple.com/videos/play/wwdc2019/224/)에서 변경점에 대해 아래처럼 발표를 한다.

> '여러분, 새로운 모달 프레젠테이션 스타일입니다! 사용자에게 더 많은 비주얼 컨텍스트를 제공합니다. 인터랙티브하게 dismiss 할 수도 있습니다. 멋지죠? 어떻게 쓰면 되나고요? 그냥 아무것도 안하면 됩니다. 왜냐면 저희가 기본 값을 이걸로 바꿔버렸기 때문이죠. 예전처럼 동작하게 만들고 싶으시다고요? 코드를 한 줄만 추가해주면 됩니다!'

예전처럼 동작하려면 코드를 추가해야 한다니! 그리고 이걸 이렇게 기쁜 표정으로 자랑하듯이 발표하다니. 이런 태도, 뻔뻔함이 애플의 상징처럼 느껴져서 황당하면서 엄청 재밌었다. Breaking change 까지는 아니지만 이번 모달의 변화는 거의 에러 못지 않게 기존 UI와의 호환성을 깨뜨리거나 유저 플로우에 큰 영향을 준다. 특히 모달을 벗어나는 경로가 새로 생긴 것에 대한 추가 작업이 필수이다.

예전에는 모달 화면을 나갈 수 있는 유일한 방법은 앱 개발자가 추가한 코드를 통해서 밖에 없었다. 하지만 pageSheet 모달을 사용하면 드래그 제스처가 자동으로 추가된다. 여기에 개발자가 관여할 수 있는 방법은 두 가지가 있다. UIViewController의 [`isModalInPresentation`](https://developer.apple.com/documentation/uikit/uiviewcontroller/3229894-ismodalinpresentation)을 true로 하면 아예 유저가 드래그로 dismiss 할 수 없게 된다. 또는 UIAdaptivePresentationControllerDelegate에 새롭게 추가된 

```swift
func presentationControllerShouldDismiss(_ presentationController: UIPresentationController) -> Bool
``` 
에서 지정할 수도 있다. 

마지막으로 유용한 델리게이트 하나는 

```swift
func presentationControllerDidAttemptToDismiss(_ presentationController: UIPresentationController)
```

`isModalInPresentation`이 true거나 presentationControllerShouldDismiss 메서드가 false 를 리턴하면 유저의 드래그 dismiss가 실패하면서 이 메서드가 불린다. 사용자가 작업해 둔 내용물이 있을 때 실수로 날리지 않게 이때 정말 화면을 나갈 것이냐를 확인하는 얼럿이나 액션 시트를 띄워주면 된다.

<img src="/assets/posts/ios13-modal-dismiss.gif" width="220" />

## 2️⃣ @propertyWrapper

Swift 5.1에 추가된 [프로퍼티 래퍼](https://github.com/apple/swift-evolution/blob/master/proposals/0258-property-wrappers.md)가 매우 유용했다. 프로퍼티 래퍼는 변수의 저장소, 캐싱 전략, 바운딩 로직 등등 변수의 특성과 관련된 로직을 분리하여 재사용할 수 있게 해주는 기능인데 총 4가지의 프로퍼티 래퍼를 만들어서 썼다.

- [SharedDefaults](https://gist.github.com/nsoojin/93f0dcd9464cf9cff6516c095723e106): UserDefaults에서 값을 가져오는 래퍼.
- [SharedKeychain](https://gist.github.com/nsoojin/93f0dcd9464cf9cff6516c095723e106): Keychain에서 값을 불러오는 래퍼.
- [LateInitialized](https://gist.github.com/nsoojin/1c77774279e1954de5ddec1d71ad84c1): 변수를 사용하기 전에 무조건 값을 세팅해줘야 하는 래퍼. IUO를 제거할 수 있다. 전부터 [unspecified()](https://github.com/nsoojin/BookStore-iOS#using-lazy-instantiation)로 만들어서 쓰던 것을 대체했다. 굳이 lazy 키워드를 써주지 않아도 돼서 실수 유발 가능성도 없고, 원하면 immutable 하게도 만들 수 있어서 훨씬 사용성이 좋다.
- [ClampedString](https://gist.github.com/nsoojin/5b47bebc9751f9a673aaf0b1a9150114): 지정해둔 max 길이를 넘어갈 수 없는 String 래퍼

프로퍼티 래퍼는 스위프트의 [API 디자인 가이드라인](https://swift.org/documentation/api-design-guidelines/)에서 가장 중요시 하는 원칙 *Clarity at the point of use*에 딱 부합하는 기능인 것 같다.

<img src="/assets/posts/clarity-at-the-point-of-use.png" />

## 3️⃣ SF Symbols

정-말 좋다. 특히 사이드 프로젝트 앱 개발을 할 때 무료 아이콘을 쓰겠다고 이곳 저곳에서 원하는 걸 가져오다 보면 통일성도 떨어지고 무엇보다 퀄리티가 너무 아쉽다. SF Symbol은 정말 많고 다양해서 용도가 무궁무진하다. 게다가 이미 시스템 앱들이 쓰고 있다. 보안카드 위젯 앱에서도 기존에 쓰던 아이콘들을 전부 대체했고 텍스트로 쓰던 부분도 적절한 아이콘으로 교체할 수 있었다. 

SF Symbol은 두 가지 방법으로 사용할 수 있다. 첫번째는 이미지로 쓰는 것이다. `UIImage(systemName:)` 생성자를 사용해서 이미지를 만들 수 있다. 두번째는 텍스트로 쓰는 것이다. 원리가 뭔지는 모르겠지만 심볼은 SF(샌프란시스코) 폰트에 포함되어있다. 그래서 SF 폰트로 설정된 UILabel이나 UITextView 등에서 이모티콘처럼 텍스트와 함께 사용할 수 있다. 심지어 폰트 두께와 크기에 따라서 아이콘도 바뀐다. 이미지로 쓸 때도 두께와 크기를 변경할 수 있다.

관련 링크
- [SF 폰트](https://developer.apple.com/fonts/)
- [SF 심볼 설명](https://developer.apple.com/design/human-interface-guidelines/sf-symbols/overview/)
- [SF 심볼 앱 다운로드](https://developer.apple.com/design/downloads/SF-Symbols.dmg): 전체 심볼을 보거나 검색할 때 유용. 

## 4️⃣ 다크 모드 

사용자에게 이번 업데이트의 최대 하이라이트는 다크 모드겠지만 개발 관점에서 딱히 특별하거나 재밌는 것은 없었다. 속된 말로 노가다지만, 하고 나서 결과물을 보면 뿌듯하고 개운한 느낌이랄까. 어두운 배경의 새로운 색을 조합하고, xcasset에 named color를 만들어서 적용하는 것 뿐이다. 네임드 컬러는 Xcode 9부터 등장했고 iOS 11부터 지원한다. 네임드 컬러를 만들어 놓고 `UIColor(named:)` 로 생성하거나 스토리보드 컬러 피커에서 고를 수도 있어서 사용이 편리하다.

또한 다크 모드 대응의 일환으로 애플에서 [시스템 컬러와 다이나믹 시스템 컬러](https://developer.apple.com/design/human-interface-guidelines/ios/visual-design/color/)라는 것을 새로 만들었다. ‘system’ 이라는 접두어가 쓰인 색을 쓰면 자동으로 다크모드와 라이트모드에서 각각 적절한 색이 사용된다. 다이나믹 시스템 컬러라는 것은 배경, 컨텐츠용 텍스트, separator 등등 색의 사용처 기준으로 색의 이름을 지은 것이다. 색 이름이 label, secondaryLabel, tertiaryLabel, separator, link 등등 처럼 되어있어서 의미에 맞춰 사용하면 된다. 또한 다크모드 대응된 회색도 6가지 종류나 생겼다. HIG 설명에 따르면 투명도가 잘 안 먹히는 상황에서 드물게 쓰라고 하는데 이건 디자인쪽 언어인거 같아서 무슨 말인지 잘 이해가 안간다. 

개발하다가 공식 문서를 읽지 않아서 디버깅이 오래 걸렸던 이슈 하나는 다크모드 / 라이트모드 전환을 할때 어떤 뷰들만 색이 전환이 안되는 이슈였다. 알고보니 UIColor 객체를 바로 사용하면 자동으로 전환이 되지만 CGColor 값으로 사용하면 개발자가 수동으로 다크 모드 전환에 대응했어야 하는 것이다. (참고: [Standard Colors](https://developer.apple.com/documentation/uikit/uicolor/standard_colors)). 그래서 커스텀 뷰의 CAShapeLayer에 적용해놓은 borderColor와 fillColor는 다크 모드 전환이 안되고 있었다. 따라서 다크모드 전환이 될 때 불리는 [적절한 메서드](https://developer.apple.com/documentation/xcode/supporting_dark_mode_in_your_interface) 내에서 CGColor 값을 지정해주어야 한다. 

종합적으로 느낀 점은 언제나 그랬듯 애플이 기본으로 제공해주는 것들만 적절히 잘 써도 디자이너의 도움 없이도 그럴듯하게 다크모드를 적용할 수 있다.


