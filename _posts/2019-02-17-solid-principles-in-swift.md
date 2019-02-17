---
layout: post
title: "스위프트로 다시보는 SOLID 원칙: 피해야할 코딩 습관"
categories: []
tags:
- SOLID
- Swift
- iOS
type: post
draft: true
<!-- published: true -->
meta: {}
---

## 배경

컴퓨터와 회화(painting)를 전공했고 꽤 성공한 [누군가](http://paulgraham.com/hp.html)가 프로그래머는 과학자보다 화가와 공통점이 더 많다고 했다. 정말로, 프로그래밍을 잘하려는 노력을 하다보면 과학보다는 경험적으로 실력을 쌓아야하는 운동이나 미술과 비슷하다고 느껴진다. 

좋은 그림을 알아보는 능력과 그림을 잘 그리는 것이 별개이듯 코드를 보고 좋다 나쁘다를 판단할 줄 아는 것과 좋은 코드를 짜는 실력은 안타깝게도 붙어오지 않는다. 코드를 많이 보게 되고 많이 짜보고 남이 짠 코드를 유지보수 해보면서 경험적으로 판별하는 후각이 조금은 탑재된 것 같다. 하지만 내가 키보드를 두들기기 시작하면 답답함이 찾아온다. 이렇게 짜면 안된다는.. 본능적인? 경험적인? 느낌은 마구 들지만, 그렇다면 이렇게 말고 어떻게 다르게, 좋게 짜야하는지에 대한 명쾌한 답은 없는 그런 상태이다. 학창 시절에 시험을 치다보면 자신있게 답을 고르는 경우도 있지만 확실히 아닌 답을 제거한 후에 남아있는 것 중에서 고르는 경우도 있다. 확실히 아닌 것을 배제하고 나면 남아 있는 선택지에 더 많은 고민을 투자할 수 있고, 답을 선택할 확률이 높아진다. 프로그래밍으로 다시 돌아와보면, 잘못된 패턴이나 유지보수에 좋지 않은 코드의 모습을 알고 이를 피하면 좀 더 나은 코드를 쓰게 되지 않을까 하는 생각을 하게 됐다. 

작년, 회사에서 함수형 스위프트 프로그래밍 스터디를 했었다. 스터디를 시작하던 날, 공부 방향을 잡기 위해 가이드를 받고자 사내에서 함수형 스위프트 강의를 하셨던 멘토님을 초빙해 조언을 부탁했다. 그 자리에서 멘토님은 지금까지 약 1년간 내 프로그래밍 공부의 방향이 된 말을 하셨다. `"함수형 프로그래밍이 얼마나 어떻게 좋은지 비교하려면 잘 짠 함수형 코드와 잘 짠 객제 지향형 코드를 비교해야하는데 아직도 내가 객체 지향으로 잘 짠다고 할 수 없어서 비교가 어렵다."` 아.. 'OOP식'으로 매일 코딩을 한다고해서, 할 줄 아는 것이 OOP 밖에 없다고 내가 OOP를 하고 있는거구나 라고 여겼었던 내 무의식적인 생각이 뿌리부터 흔들렸다. 그래서 다시 기본부터 잘해야겠다고 생각했고 이 글은 지난 몇 개월 간 습득한 지식의 요약이다.

## 스위프트와 SOLID

SOLID는 로버트 마틴이라는 사람이 명명한 객체 지향 프로그램 및 설계의 기본 원칙 다섯 가지이다. 스위프트는 멀티패러다임 언어이지만 Foundation, UIKit 등 코코아 터치 프레임워크들은 기본적으로 OOP에 근간을 두고 있다. SOLID 원칙들과 iOS를 연관지어서 살펴보니 내가 쓰고 있는 많은 클래스들이 왜 그런식으로 만들어졌는지 알 수 있었고, 또 내가 어떻게 이 원칙들을 녹여낼 수 있는지 좀 더 와닿게 이해할 수 있었다. 본문에서는 각 원칙에 따라 만들어진(혹은 위반하고 있는) <U>iOS 프레임워크의 예시</U>와 함께 이 원칙을 잘 지키기 위해서 어떻게 코딩을 해야하는지, 특히 <U>이 원칙을 어긴 것은 어떤 형태인지</U> 중점적으로 정리했다. 앞서 말했듯, 처음부터 코드를 잘 짤 순 없으니 최소한 잘못된 코드가 어떤건지는 알고 피해가자는 컨셉이다.

## Single Responsibility Principle

단일책임 원칙은 모든 클래스는 하나의 책임만 가져야 한다는 원칙이다. 로버트 마틴은 책임을 *변경하려는 이유*로 정의했다. SRP의 가장 대표적인 위반 사례는 **Massive View Controller** 현상이다. 이런 MVC의 문제를 해결하기 위해 여러 다른 패턴들이 생겨나고 시도되고 있는데, 그 것들의 공통점은 너무 많은 역할을 하고 있는 기존의 뷰컨트롤러를 쪼개서 단일 책임만을 가지는 여러 클래스를 만드려고 하는 것이다. 

SRP를 지키기 위해 클래스를 작게 만드려는 노력을 하고 있다. 클래스가 작다는 것이 SRP의 충분 조건은 아니지만 필요 조건이다. 이 목표를 달성하기 위해 멘토님이 제시한 **10-200룰**을 염두하면서 코딩한다. 10-200룰은 함수는 10줄 이내, 클래스는 200줄 이내로 만드는 것이다. 하지만 이를 엄격하게 지키기는 쉽지 않고, 아직은 이상향으로 여기는 수준이다. 실제로는, 200줄이 넘어가면 혼자 머리 속에서 경보를 발령하고 더이상 안 늘어나게 노력하거나 리팩토링을 시도한다. 

200룰을 지키면서 개발하려면 필요한 프로퍼티, 함수나 메서드를 아무데나 만들어서 쓸 수 없게 된다. 꼭 얘가 이 곳에 있어야 하는 이유를 찾아야한다. 이 클래스에 있을 필요가 없으면 SRP 위반이다. 가장 쉽게 판단할 수 있는 방법 하나는, 함수나 메서드 내부에서 self의 프로퍼티나 메서드를 쓰고 있지 않다면 해당 클래스에 있을 이유가 없는 것이다. 또는 self의 빈도가 적을수록 클래스와 연관성이 떨어지는 것이니 클래스를 다이어트 시켜야할 상황이 오면 그런 것들을 우선적으로 다른 곳으로 옮겨야할 것이다.

## Open-closed Principle

개방폐쇄 원칙은 확장에는 열려 있으나 변경에는 닫혀있어야 한다는 원칙이다. 열려 있다는 것은 기능 추가나 변경을 할 수 있어야 한다는 것이고 닫혀있다는 것은 기능 추가를 할 때 그 모듈을 쓰고 있는 코드들을 줄줄이 수정하지 않아야 한다는 것이다. OCP 위반의 대표적인 예는 **어떤 타입에 대한 반복적인 분기문**이다. 즉, 하나의 enum에 대해 여러 군데에서 반복적으로 if/switch문을 쓰고 있다면 고민을 해봐야한다. 왜냐하면 이런 경우, 기능 추가는 case를 한줄 추가하는 것만큼이나 쉽지만 그렇게 하는 순간 해당 enum을 스위칭하고 있는 모-오든 코드를 찾아서 수정해줘야 한다. exhaustive하게 짰다면 그나마 컴파일러의 도움을 받을 수도 있겠지만 default문을 추가했다면 그것도 여의치 않다.

OCP 원칙은 if/switch를 최대한 사용하지 않기 위해 노력하면서 연습해볼 수 있다. 모든 분기문을 없애는 것은 아니고(불가능하고) enum 같이 타입을 분기하는 지점에 대해서. 처음에 이 얘기를 들었을때는 말도 안된다고 생각했다. if문은 프로그래밍의 가장 기본 아닌가? 어떻게 if/switch 없이 분기를 할 수 있단 말인가? 처음엔 너무 어색하고 답답했지만 시도해보면서 어떤식으로 내 코드에 도움이 되는지 조금씩 느끼고 있다. 게다가 앞서 언급한 10-200룰을 지키려면 어쩔수 없는 선택이기도 하다. switch문 하나를 쓰는 순간 기본적인 syntax만으로 거의 10줄이 되고, if문도 라인수가 많이 늘어나기 때문에 OCP에 대해 고민할 수 밖에 없어진다.

enum + switch 조합 대신에 쓸 수 있는 방법 두 가지를 소개한다. 첫번째로 protocol을 만들고 상속받아 쓰는 방법이다. 상세한 코드는 이 글의 범위를 벗어나는 것 같아서 생략하지만 [이 블로그](https://medium.com/swift-fox/refactoring-replace-enum-with-polymorphism-c4803baeba07)에 있는 내용을 참고하면 되겠다. 또 한가지 간단한 방법은 딕셔너리를 활용하는 것이다. 최근 사이드 프로젝트를 하면서 실제로 사용해본 방법이다.

기존 코드
```swift
switch reason {
  case .initializing:
    self.instructionMessage = "Move your phone".localized
  case .excessiveMotion:
    self.instructionMessage = "Slow down".localized
  case .insufficientFeatures:
    self.instructionMessage = "Keep moving your phone".localized
  case .relocalizing:
    self.instructionMessage = "Move your phone".localized
}
```

바꾼 코드
```swift
//적절한 곳에 딕셔너리 생성
let trackingStateMessages: [TrackingState.Reason : String] 
                         = [.initializing.        : "Move your phone".localized,
                            .excessiveMotion      : "Slow down".localized,
                            .insufficientFeatures : "Keep moving your phone".localized,
                            .relocalizing         : "Move your phone".localized]

//switch문 대체
self.instructionMessage = trackingStateMessages[reason]
```

## Liskov Subtitution Principle

리스코프 치환 원칙은 상위 타입(부모 클래스)을 하위 타입의 인스턴스(자식 클래스)로 바꿔도 프로그램의 동작을 해치지 않아야 한다는 원칙이다. 정의는 어렵지만 이를 지키기 위한 방법은 의외로 간단하다. **자식이 부모의 동작을 제한해서는 안된다.** 전형적인 위반 사례로는 직사각형을 상속받아서 만든 정사각형 클래스를 생각하면 된다. 정사각형은 너비와 높이가 같아야 하기 때문에 너비와 높이를 자유롭게 바꿀 수 있는 직사각형 부모 클래스의 동작을 제한해야만 원하는 동작을 만들 수 있다. 이런 경우가 LSP의 위반이다.

iOS 프레임워크에서도 LSP 위반 사례를 찾아 볼 수 있다. 
```swift
var label = UILabel(frame: .zero)
var button = UIButton(frame: .zero)
var segmentedControl = UISegmentedControl(frame: .zero)
var textField = UITextField(frame: .zero)
var slider = UISlider(frame: .zero)
var switchButton = UISwitch(frame: .zero)
var activityIndicator = UIActivityIndicatorView(frame: .zero)
var progressView = UIProgressView(frame: .zero)
var stepper = UIStepper(frame: .zero)
var imageView = UIImageView(frame: .zero)

let views: [UIView] = [...] //위 뷰들을 UIView 어레이에 저장

views.forEach { $0.frame.size.height = 30 } //뷰들의 height를 30으로 변경

let height = views.reduce(0) { $0 + $1.frame.height } 
print(height) //과연 결과는?
```

10개의 UIView subclass들의 높이를 30으로 바꿨으니 300이 되길 예상할수도 있지만 실제 결과는 272다. 왜냐하면 일부 뷰들은 intrinsicSize를 마음대로 바꿀수 없게 되어 있기 때문이다. 이렇게 UIView(부모 타입)의 동작(높이를 바꾸는 것)을 제한하는 일부 뷰들(UIProgressView, UISwitch 등)이 바로 LSP 위반이다. 

또한 iOS 개발을 하다보면 아래의 코드를 본 적이 있고 만들어낸 적도 있을 것이다. 이처럼 부모의 함수를 오버라이드해서 퇴화시켜 버리는 함수는 만들면 LSP 위반이다.

```swift
required init?(coder aDecoder: NSCoder) {
  fatalError("init(coder:) has not been implemented")
}
```

상속은 객체 지향 프로그래밍의 중요한 부분이고 잘 쓰면 유용하지만 잘못된 상속을 만들면 문제가 발생할 수 있다. 반면 때에 따라서는 LSP를 위반함으로써 편리함과 단순함을 얻게 될 수도 있다. 결론적으로, LSP를 무조건 지키려고만 하면 비효율적이고 너무 자주 위반하면 예상하지 못한 결과들 때문에 안정성을 해치게 된다. 그러므로 상속을 만들때는 LSP 위반인지 아닌지를 숙지하고 사용하는 것이 좋겠다.

## Interface Segregation Principle

인터페이스 분리 원칙은 클라이언트가 자신이 이용하지 않는 메서드에 의존하지 않아야 한다는 원칙이다. 클라이언트라 함은 기능을 제공하는 클래스(서버라고 함)를 사용하는 쪽을 의미한다. A라는 클래스가 내부적으로 B의 인스턴스를 만들어서 사용하던지 아니면 상속을 통해 B라는 클래스의 기능을 사용하고 있다고 하자. 그러면 A는 B의 클라이언트다. 이 경우 B의 동작이 바뀌거나 A가 사용하지 않는 메서드가 바뀌었다고해서 A도 바뀌어야 하면 안된다는 것이다. 즉 의존도를 낮춰야 한다는 것인데, 이는 서버 B가 클라이언트 A에게 최소한의 인터페이스만을 정의하여 제공하고 A는 이 인터페이스를 기준으로 개발함으로써 달성할 수 있다.

iOS와 스위프트에서도 수많은 ISP 사례들을 찾아볼 수 있다. 테이블뷰는 UITableViewDataSource와 UITableViewDelegate로 분리해서 기능을 제공한다. 또한 [Swift Standard Library](https://developer.apple.com/documentation/swift/swift_standard_library/basic_behaviors)에도 Comparable, Equatable, Hashable, Encodable, Decodable 등 프로퍼티/메서드 한 두 개짜리 작은 프로토콜이 많이 존재하고 여러 struct나 class들에서 사용되고 있다. 

ISP 원칙을 지키려면 클라이언트의 입장과 서버의 입장 둘 다 고려해야한다. 서버의 입장에서는 자신의 기능을 잘 쪼개서 제공하는 것이 좋다. SRP와 비슷하게 느껴지기도 한다. 클라이언트가 필요로 하는 최소한의 인터페이스만 제공하는 프로토콜을 여러 개 만드는 것이 하나의 커다란 프로토콜을 제공하는 것 보다 낫다.

클라이언트의 입장에서는 구체화된 타입을 직접 쓰지 않고 프로토콜을 기준으로 개발해야 한다. UITableView는 내가 UITableViewDataSource를 구체화 시킨(상속시킨) 클래스를 모른다. 프로토콜을 기준으로 기능 구현을 했기 때문에 나의 데이터소스가 UIViewController이든, 다른 클래스이든 그런 것에 의존성 없이 잘 돌아가게 된다. 또한 스위프트 개발자로서 스위프트나 iOS 프레임워크에서 사용되는 프로토콜들을 숙지해서 최대한 구체적인 클래스와의 의존성을 낮추는 것이 좋다. 가령 어떤 곳에서 String을 쓰고 있지만 사실은 스트링의 hashValue만 필요한 것일 수도 있다. 이럴때는 String을 쓰지 않고 Hashable 타입을 쓰는 것이 더 좋다. 

## Dependency Inversion Principle

의존관계 역전 원칙에 따르면 모듈은 추상화에 의존해야지 구체화에 의존하면 안된다. DIP도 정의만 보면 이해가 쉽게 되지는 않는다. DIP는 클래스 간의 의존 관계를 어떻게 만들어줄 것인가에 대한 기준을 제공한다. 구체적인 부분이 추상적인 부분에 대해 의존적이어야지, 그 반대가 되면 안된다는 것이다. 그리고 의존성은 단방향인 것이 좋다. 만약 A, B, C 클래스가 순환적으로 의존성을 가지고 있다면 A, B, C는 독립적으로 사용되지 못하고 무조건 같이 붙어다녀야 하기 때문에 코드가 복잡해지고 수정이 발생했을 때 여러 곳을 고쳐야 한다.

UIViewController가 DIP의 사례 중 하나이다. 우리는 UIViewController를 상속받은 MyViewController를 만들어서 쓴다. 그러면 UIViewController를 사용하는 여러 시스템(UINavigationController와 같은 Container View Controllers, 스토리보드, present & dismiss, custom transition 같은 기능 등)에서 MyViewController를 화면에 보여주고, viewDidLoad와 같은 함수를 호출해주기도 하면서 우리가 원하는 대로 잘 동작한다. 이는 UIKit이 구체화된 MyViewController가 아닌 추상화된 UIViewController에 의존적으로 동작하고 있기 때문이다.

그렇다면 무엇이 추상적인 부분이고 무엇이 구체적인 부분일까? DIP 원칙이 가장 *추상적*으로 들리면서, 행동으로 곧바로 옮기기 어려운 이유가 바로 추상 & 구체를 구분 짓는 능력에서 경험이 필요하기 때문인 것 같다. 원리로 보면 추상적인 부분은 자주 변경되지 않는 부분, 로직의 뼈대 부분이고 구체적인 것은 자주 변경되는 부분이다. 개발을 할때 어떤 부분이 추후에 변경될지, 변경되지 않을지 파악할 수 있을까? 쉽지 않을 것 같다. 시행착오도 겪어야 하고 경험이 많이 필요한 것 같다.

## 결론

이 글의 목표는 SOLID 대원칙을 완벽히 이해하려는 것이 아니다. 게다가 모든 원칙을 다 지키는 코드만 짜려고 하는 것은 비효율적이다. 예를 들어 모든 것을 OCP로만 짜려고 하면 너어무 불편하다. 분기문을 쓰지 않으려고 매번 프로토콜을 만들고 상속을 해야한다면 개발하는 시간도 오래걸리고 불필요한 복잡성만 증가하는 꼴이다. 따라서 적절한 트레이드 오프가 필요하다. 그리고 다섯 가지 원칙 중에서 SRP와 OCP가 단순하지만 가장 지키기 어렵고 끊임없이 노력해야하는 부분인 것 같다. 마치 운동선수에게는 체력이고 화가에게는 선 그리기와 같지 않을까 싶다. **무엇이 정답인지 모르고 헷갈릴때 확실히 정답이 아닌 것을 지워내는 것처럼, 위 원칙을 어기는 코드 습관을 파악해서 일단 그것부터 제거하고 개선해보려고 한다.**

끝.
