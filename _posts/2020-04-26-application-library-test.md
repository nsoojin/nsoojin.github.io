---
layout: post
title: "XCTest 소요시간 단축하기"
categories: []
tags:
- xctest
- application test
- library test
- ios testing
type: post
draft: true
published: true
meta: {}
---

얼마전까지 XCTest를 유닛 테스트와 통합 테스트(Xcode의 UI Testing)로만 구분하고 있었는데, 유닛 테스트 또한 런타임 작동 방식에 따라 Application test와 Library test로 나눌 수 있다는걸 알게 됐다. 둘의 차이점과 쓰임새를 정리했다.

## Application Test vs Library Test

<br>
<img src="/assets/posts/application-library-test.png" />

유닛 테스트 타켓의 Host Application 항목에서 앱 executable을 선택하면 application test가 되고 None을 선택하면 library test가 된다. Xcode에서 유닛 테스트 타겟을 만들 때 초기 설정을 변경하지 않았다면 기본값으로 Host Application이 선택된다. 그래서 지금까지는 별 생각없이 대부분의 테스트를 Application test로 짜고 있었다. 그러나 목적에 따라 이 둘을 구분해서 사용하면 여러 이점이 있다.

## Application Test

iOS 앱과 관련된 부분(UIViewController, UIWindow, UIView 등)을 검사하는 유닛 테스트는 application test 번들에 포함시켜야 한다. Application test는 실행하기 위한 호스트 앱이 필요하고, 호스트 앱을 설치할 iOS 시뮬레이터도 필요하다.

Application test의 단점은 아래와 같다.
- [앱 라이프사이클](https://developer.apple.com/documentation/uikit/app_and_environment/managing_your_app_s_life_cycle)이 돌게 되는데 타이밍 이슈가 발생하여 테스트 결과가 달라질 수도 있다.
- 시뮬레이터는 한번에 하나의 호스트 앱만 실행할 수 있기 때문에 application test 타겟은 하나의 시뮬레이터에서 병렬(parallel) 테스팅이 작동하지 않는다. 

## Library Test

Library test는 앱과 상관없는 순수 로직을 테스트하기 위해 사용한다. 이름 그대로 라이브러리를 테스트하는 것이라고 생각하면 된다. Library test는 호스트 앱을 시뮬레이터에 설치할 필요가 없어서 테스트가 더 빠르고 이따금씩 동작이 불안정한 시뮬레이터의 영향을 덜 받는다. 

반면 library test에서는 작동하지 않는 iOS API가 좀 있다. 그런 경우에는 application test에서 검사해야 한다.
- 유닛 테스트에서 유저의 버튼 탭을 흉내내기 위해 사용하는 `UIControl`의 `func sendActions(for controlEvents: UIControl.Event)`는 동작하지 않는다. 
- 임의의 UIWindow를 keyWindow로 만들 수 없다. 호스트 앱도 없기 때문에 UIWindow 자체가 없다. 그래서 UIView를 렌더링한 다음 검사하는 [snapshot test](https://github.com/uber/ios-snapshot-test-case)가 불가능하다.
- Keychain 관련 API

(등등 더 있을수 있다.)

## Library Test를 쓰면 좋은 이유

Application test는 호스트 앱의 프로세스에 주입되기 때문에 시뮬레이터에 앱이 설치되고 실행(app launch)까지 된 후에야 테스트 코드가 돌기 시작한다. 반면에 library test는 앱에 의존하지 않아서 앱 설치 없이 iOS 시뮬레이터 내 `xctest` executable(`$DEVELOPER_DIR/Platforms/iPhoneSimulator.platform/Developer/Library/Xcode/Agents/xctest`)이나 macOS의 `xctest` 커맨드라인의 프로세스(`/usr/bin/xctest/`)에서 실행된다. 따라서 기존의 application test를 library test로 전환하면 `전환한 타겟 갯수 * 앱 설치 및 실행 소요시간` 만큼의 테스트 시간을 줄일 수 있다. 또한 호스트 앱을 기다릴 필요 없어 병렬로 빠르게 테스팅 할 수 있다.

## Library Test로 전환하는 방법

먼저 테스트 타겟의 General 탭에 가서 Host Application을 None으로 변경하고 테스트를 실행해본다. 문제가 발생하면 다음과 같이 해결한다.

### 1. 테스트 번들이 실행되지 않을 때

아래와 같은 런타임 에러를 낸다.

```
Library not loaded. 
(중략)
Reason: image not found
```

Application test는 호스트 앱에 주입이 되기 때문에 앱 타겟에 링크된 라이브러리를 사용할 수 있다. 하지만 library test는 독립적인 프로세스에서 실행이 되기 때문에 테스트 타겟에 외부 의존성을 직접 링크해줘야한다. 코코아팟을 사용하고 있다면 Podfile을 열어 테스트 타겟에도 앱이 사용하고 있는 외부 의존성을 추가해준다.

### 2. 테스트 케이스가 실패할 때

실행은 되지만 테스트케이스가 실패한다면 iOS 앱에 의존성 있는 API를 사용했을 확률이 크다. 따라서 타겟을 분리하고 library test에서 실패하는 테스트는 application test로 옮긴다. 즉 iOS 앱이 필요한 테스트만 application test에서 실행하고 나머지는 library test로 실행하면 테스트 소요시간을 줄일 수 있다