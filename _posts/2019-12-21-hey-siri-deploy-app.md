---
layout: post
title: "시리야 앱 배포해줘"
categories: []
tags:
- siri shortcut
- deploy app
- siriops
type: post
published: true
meta: {}
---

<img src="/assets/posts/siri01.png" width="300" />
<p style="text-align: left;"><small>시리 응답이 웰케 애잔하냐...</small></p>

## 배경

올해 초 젠킨스 CI 서버 구축을 처음 해봤고, 그걸 계기로 자동화에 관심이 생겨 일 년 동안 틈틈이 팀 내 배포/개발 프로세스를 개선시켜왔다. 사실 관심이 생겼다는 말은 빈약한 표현이고 나는 내가 이 정도로 의미없는 반복 작업을 싫어하는 사람인줄 몰랐다. 아무 생각없이 할 수 있는 반복 작업도 가끔씩하면 정신 건강에 좋다고 생각했었다. 하지만 개발 프로세스에 녹아있던 불필요한 수작업이 하기 싫어서 어떻게든 방법을 찾아서 팀원들을 설득하고 바꿔버렸다. 그리하여 여러 사람의 손을 거쳐야했던 반복적이고 귀찮은 작업을 대부분 자동화했고, 이제는 시리도 우리 앱을 배포할 수 있을 정도로 간단해졌다.

## 사전 준비

일단은 당연하게도 앱 배포 단계가 자동화되어 있어야 한다. 팀마다 다르겠지만 보통 앱 배포는 최소한 `버전업`, `빌드`, `dSYM 처리`(크래시 수집 툴에 업로드)를 해야하고 앱스토어용으로 `스크린샷 생성`하거나 사내 배포용 엔터프라이즈 앱이라면 빌드 결과물을 `사내 인프라에 업로드`하는 등의 일련의 작업이 있다.

베포 자동화에 큰 도움이 된 것이 [fastlane](https://fastlane.tools)이다. 가독성 좋은 API를 써서 빌드 시나리오를 쉽게 만들 수 있다. 오픈소스 플랫폼이라 여러 사람들이 만든 다양한 플러그인도 있어서 안되는 것이 거의 없다. 특히, xcconfig 파일을 수정할 수도 있고 git 작업도 쉽게 할 수 있고 필요에 따라서는 쉘 스크립트를 추가할 수도 있다. 그러면 fastlane 명령어 한 줄만으로 배포를 할 수 있게 된다.

그리고 빌드 서버가 필요하다. 요즘은 클라우드 기반 CI 서비스가 많다. 개발자 컨퍼런스를 가봐도 CI 서비스를 하는 스타트업에서 꼭 부스를 운영하고 홍보한다. [travis-ci](https://travis-ci.org), [circleci](https://circleci.com), [bitrise](https://www.bitrise.io) 등이 있지만 이 글에서는 자체 서버를 구축해야하는 무료 CI 인 [Jenkins](https://jenkins.io)를 기준으로 설명한다.

## 시리 숏컷 연동

사실 시리를 연동하는 단계는 가장 쉬운 단계이다. CI 서버를 통해 배포를 자동화하는 "사전 준비"에 99%의 시간과 노력이 들어간다. 실제로도 프로세스 자동화는 1년 내내 작업했고 시리 연동은 반나절 밖에 안걸렸다. 가장 이상적으로는 젠킨스 job의 `Build Now` 버튼만 클릭하면 배포에 필요한 작업을 자동으로 다 하고 짜잔하고 앱이 배포되는 것이다. 

이런 배포 시나리오가 완성되면, 젠킨스 job의 Configure에 들어가서 Build Trigger(빌드 유발)에서 `Trigger builds remotely`를 체크하고 토큰 값을 지정해준다.

<img src="/assets/posts/siri02.png"/>

그러면 이제 *http://JENKINS_URL/job/JOB_NAME/build?token=TOKEN_NAME*으로 POST를 날려주기만 하면 배포가 실행된다. 하지만 이것만 설정하면 젠킨스 서버 authentication을 처리해줘야 하는데 귀찮다면 [Build Authorization Token Root](https://plugins.jenkins.io/build-token-root) 플러그인을 사용하면 위에 설정해준 토큰만으로 접근할 수 있다. 

이렇게 젠킨스 플러그인까지 설치해줬다면 아래 명령어를 터미널에 입력하면 배포가 시작된다.

```
> curl -X POST http://JENKINS_URL/buildByToken/build?job=JOB_NAME&token=TOKEN_NAME
```

여기까지만 해줘도 배포가 훨씬 덜 귀찮은 작업이 됐다. 하지만 시리 숏컷이 처음 나왔을때부터 시리한테 배포를 시켜보는 것이 로망이었기 때문에 한 단계 더 나가봤다. 아이폰에서 시리 숏컷(단축어) 앱을 실행하여 숏컷을 하나 생성한다. 아래처럼 Get Contents of URL 에다가 배포 URL을 넣고 POST를 선택해주고 내가 원하는 명령어를 입력해주면 끝이다.

<img src="/assets/posts/siri03.png" width="300" />

## 느낀점

시리 숏컷을 처음 써봤는데 상상 이상으로 잘 만들어져 있어서 놀라웠다. 간단한 인터페이스로 할 수 있는 일이 무궁무진하다. 심지어 if문, 반복문, 변수 할당, 사용자 인풋 받기, 얼럿 띄우기 등등이 가능해서 흡사 앱 개발처럼 느껴진다. 친구한테 알려주니 우스갯소리로 스크래치말고 시리 숏컷으로 프로그래밍 배워야하는거 아니냐고 그랬는데 정말 불가능한 일도 아닌거 같다. 때마침 [긱뉴스](https://news.hada.io)에서 [No Code is New Programming](https://jeremyqho.com/no-code-is-new-programming)라는 글을 읽게 되었는데, API가 정말 고도화되면 맨 마지막 단계에서는 코드 없는 인터페이스가 탄생한다는 것이고 이게 프로그래밍의 미래 모습이라는 것이다. 시리 숏컷이 이런 개념에 포함되는게 아닌가 싶다. 글이 배포 자동화로 시작해서 시리 숏컷 찬양으로 끝나게 되었는데, 시리 숏컷이 단순히 내 아이폰에 있는 앱을 실행시켜주는게 다가 아니라 여러 API*(즉, iOS 앱)*를 코드 없이 조합할 수 있다는 것에 큰 영감을 얻게 되었다.

<img src="/assets/posts/siri04.png" width="300" />
<p style="text-align: left;"><small>"알겠습니다.."가 너무 애잔하여 응답을 바꿨다.</small></p>