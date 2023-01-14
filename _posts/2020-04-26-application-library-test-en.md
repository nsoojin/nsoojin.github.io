---
layout: post
title: "Reducing Time Spent for an XCTest"
categories: []
tags:
- xctest
- application test
- library test
- ios testing
type: post
published: true
meta: {}
thanks: 
- 류성두 님
---

The XC test was only divided into unit tests and integration tests until recently, and I found out that unit tests can also be divided into application tests and library tests according to how they operate runtime. I summarized the differences and the usage of the two.

## Application Test vs Library Test

<img src="/assets/posts/application-library-test.png" />

If you select the app executable from the Host Application item from the unit test target, it becomes an application test, and if you select None, it becomes a library test. If you did not change the initial settings when creating a unit test target, the Host Application will be selected as default.
So until now, I made most of the tests as application tests without giving them much thought.
But if you use them according to each purpose there is a clear advantage.

## Application Test

A unit test that tests parts related to the iOS app(UIViewController, UIWindow, UIView, etc) should be included in the application test bundle. The application test bundle requires a host app to run the test, as well as an iOS simulator that would install the host app. 

The disadvantages of an Application test are as follows.
- There is an app lifecycle, and a timing issue might occur, given different test results.
- Since the simulator can only run one host app, the application target cannot be tested using parallel testing on one simulator.

## Library Test

A library test is used to test logic unrelated to the app.
Library tests do not require the host apps to be installed on the simulator, so they are less affected by simulators with unstable behavior.

However, there are some iOS APIs that do not work in library tests. 
In that case, it should be checked in the application tests.
- UIControl's func sendActions(for controlEvents: UIControl.Event), which is used to imitate the button taps of a user in a unit test does not work.
- You cannot make a random UIWindow into a keyWindow. There is no UIWindow because there is no host app. Therefore, snapshot testing which tests after rendering a UIView is impossible.
- Keychain-related API

(and there could be more.)

## Why It's Good to Use the Library Test
Because the Application test is injected into the host app process, the test code only begins to spin after the app is installed and launched within the simulator. On the other hand, the library test is not dependent on the app, so it runs on `xctest` executable(`$DEVELOPER_DIR/Platforms/iPhoneSimulator.platform/Developer/Library/Xcode/Agents/xctest`) within the iOS simulator without installing the app, or within the `xctest` commandline process(`/usr/bin/xctest/`). Therefore, switching the application test to a library test can reduce the time spent in testing by the number of targets switched * time spent for installing and running the app. Also, testing can be done in parallel since it doesn't have to wait for the host app.

## How to Switch to a Library Test

First, go to the General tab of the test target, change the Host Application to None and then try running the test.  
If a problem occurs, solve it as follows.  


### 1. 테스트 번들이 실행되지 않을 때

아래와 같은 런타임 에러를 낸다.

```
Library not loaded. 
(중략)
Reason: image not found
```

Application test는 호스트 앱에 주입이 되기 때문에 앱 타겟에 링크된 라이브러리를 사용할 수 있다. 하지만 library test는 독립적인 프로세스에서 실행이 되기 때문에 테스트 타겟에 외부 의존성을 직접 링크해줘야한다. 코코아팟을 사용하고 있다면 Podfile을 열어 앱이 사용하고 있는 외부 의존성을 테스트 타겟에도 추가해준다.

### 2. 테스트 케이스가 실패할 때

실행은 되지만 테스트케이스가 실패한다면 iOS 앱에 의존성 있는 API를 사용했을 확률이 크다. 따라서 타겟을 분리하고 library test에서 실패하는 테스트는 application test로 옮긴다. 즉 iOS 앱이 필요한 테스트만 application test에서 실행하고 나머지는 library test로 실행하면 테스트 소요시간을 줄일 수 있다

### 1. When the test bundle isn't running

It makes a runtime error as seen below.

```
Library not loaded. 
(...)
Reason: image not found
```

Because application tests are injected into the host app, they can use libraries that are linked to the app target. 
But because library tests run on an independent process, we need to directly link an external dependency on the test target. If you're using CocoaPods, open Podfile and add the external dependency that the app is using into the test target as well.

### 2. When the test case fails

If the test runs but the test case fails, it's highly likely that a dependent API is used on the iOS app.
Therefore, the target is separated and the test that fails in the library test is moved to the application test.
In other words, running tests that need iOS apps in the application test and running the rest as library test can reduce time spent in testing.
