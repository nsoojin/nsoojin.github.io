---
layout: post
title: "앱 안정성을 향한 끊임없는 여정"
categories: []
tags:
- tests
- app stability
- regression
type: post
published: true
meta: {}
---

서너 달 전 커머스 스타트업의 CTO인 아는 동생이 고민 상담을 해왔다. 운영 중인 앱에서 비슷한 버그가 여러 번 발생하고 중요한 기능이 어느날 갑자기 작동을 안하는 일이 반복되는데, 근본적으로 어떻게 개선할 수 있냐는 질문을 했다. 마침 올해 우리 팀의 눈에 띄는 성과가 앱의 안정성을 높인 일이다. 코드와 인프라와 개발 프로세스를 꾸준히 발전시키려는 노력을 한 끝에 앱의 안정성이 꾸준히 개선되고 있다. 동생네 팀의 현 상황을 좀 더 자세히 들어본 후 단기, 장기적으로 실행할 수 있는 여러 방안을 제안했다.

앱 안정성이라는 것은 사람마다, 팀마다 다르게 해석할 수 있기 때문에 측정하는 방법도 다양하다. 사용자 입장에서 보면 crash-free session, crash-free user 비율 등을 추적할 수 있다. 사용자에게 직접적인 영향을 주는 이런 수치는 출시 이후에 발생하는 일에 대한 정보다. 하지만 출시 이전부터 측정할 수 있는 안정성에 대한 수치도 매우 중요할 뿐더러 이 둘은 깊게 연관돼 있다. 일반적으로 기능 개발, 기능 테스트, 회귀 테스트를 거쳐 최종 사용자에게 코드가 배포된다. 회귀 테스트 도중, 혹은 배포 이후 발견되는 [회귀 버그(regression bug)](https://ko.wikipedia.org/wiki/회귀_테스트)는 앱 안정성에 매우 치명적이다. 직접적으로 코드를 수정하지 않았다고 여겼던 부분에서 예상 못한 버그가 발생했다는건 1차적으로는 코드의 문제지만 코드 리뷰, 자동화 테스트, QA를 거친 후에 발견됐다는건 우리 팀의 프로세스에 버그가 빠져나갈 구멍이 있다는 뜻이므로 각 단계별로 개선점을 찾아서 메꿔야 한다. 각 단계별 개선 방법과 목적이 다르므로 그에 맞춰 대책을 도입하고 장기적으로 유지해야 한다.

## 1. 코드 품질을 높이고 테스트하기

가장 먼저, 버그의 근원지인 코드의 품질을 개선하고 테스트를 작성하는 것이 가장 확실하고 장기적으로 유지 가능한 해결책이다. 엉클밥이 말하길 *"수학에서는 무언가가 옳다는걸 증명하지만 과학에서는 무언가가 틀렸다는걸 증명한다. 과학은 테스트 커버리지가 높기 때문에 신뢰할 수 있다. 소프트웨어는 과학이기에 테스트로 우리의 프로그램이 잘못됐다는걸 증명할 수 있다. 고로 커버리지가 낮은 소프트웨어는 신뢰할 수 없다."*([원문](https://twitter.com/unclebobmartin/status/1311295606179102720?s=20))고 했다. 내가 만든 소프트웨어가 의도한대로 동작하는걸 확인할 수 있는 방법은 테스트를 작성하는 것이다. 앱 개발 환경에서 테스트와 아키텍처는 밀접한 관계가 있다. iOS 앱 아키텍처의 기본값이라고 할 수 있는 MVC는 테스트하기 썩 적합하지 않다. 이런 문제를 해결하기 위해 다양한 아키텍처가 생겨났다. 만약 MVC가 현재 주 아키텍처라면, 중장기적인 계획을 세워 [다른 아키텍처](https://academy.realm.io/posts/krzysztof-zablocki-mDevCamp-ios-architecture-mvvm-mvc-viper/)로 리팩토링을 감행하자. 또한 [좋은 아키텍처는 개발자의 실수를 줄여주고 올바른 방향으로 코딩하는걸 더 쉽게 해준다.](https://soojin.ro/blog/pragmatic-programmer)

테스트는 [속도와 통합 정도를 기준으로 종류가 나뉘므로](https://martinfowler.com/articles/practical-test-pyramid.html) 상황과 사정에 맞게 우선 순위를 두고 도입하자. iOS의 XCTest 프레임워크는 유닛 테스트와 UI 테스트를 지원한다. 유닛 테스트는 모듈 단위로도 실행할 수 있기 때문에 일반적으로 실행 속도가 매우 빠르다. 유닛 테스트는 추가적으로 [Application Test와 Library Test로 구분되니](https://soojin.ro/blog/application-library-test) 기술적으로 적합한 것을 선택하면 된다. 또한 엑스코드가 유닛 테스트의 결과로 코드 커버리지를 알려주기 때문에 DerivedData에 생성되는 xcresult 파일을 읽어서 모듈별 커버리지를 쉽게 수집할 수 있다. 반면에 UI 테스트는 앱 전체를 빌드한 후 시뮬레이터에서 실행해야 해서 일반적으로 테스트 수행 시간이 길지만, 코드 일부분이 아니라 마치 사람이 앱을 사용하는 것처럼 기능을 테스트할 수 있기 때문에 중요한 기능이 정말 제대로 동작하고 있는지 확실하게 검수할 수 있다는 장점이 있다.

코드 리뷰도 코드 품질을 높이는 방법 중 하나다. 코드 리뷰를 잘하는 법은 구글이 공개한 [내부 가이드](https://google.github.io/eng-practices/)가 많은 도움이 된다. ([번역 요약본](https://soojin.ro/review/))

## 2. 인프라 구축하기

### 자동화 인프라

앱을 지속적으로 개발하고 배포하고 운영하려면 CI/CD라고 부르는 자동화 인프라가 필요하다. 가장 기본적으로 배포 자동화, 테스트 자동화를 할 수 있다. 매 커밋마다 배포를 할 수도 있고 매일 특정 시간에 배포를 할 수도 있고 필요에 따라 개발자가 원할 때 배포를 할 수 있다. [Siri한테 시킬 수도 있다.](https://soojin.ro/blog/hey-siri-deploy-app) 또한 풀리퀘스트 기반의 개발 프로세스를 가진 팀이라면 풀리퀘스트마다 테스트를 자동으로 실행시켜서 새 코드가 메인 브랜치에 작업이 합병되기도 전에 기존 기능이 망가지지 않았는지 확인할 수 있다. 자동화는 개발과 연관된 모든 프로세스의 기반이 된다. 자동화가 돼있는 만큼 개발자가 반복적인 작업에 시간과 노력을 낭비하지 않을 수 있고, 자동화된 테스트는 회귀 버그를 잡아낼 수 있는 1차적인 방어선이므로 인프라를 꼭 구축하자.

### 실시간 품질 모니터링

사용자에게 신기능을 배포한 후 서비스 운영 도중 문제가 발생했을 때 신속/정확하게 대응하기 위해서는 모니터링 기반이 갖춰져 있어야 한다. 최종 사용자가 느끼는 서비스 품질을 나타내는 수치를 Quality of Experience Metrics(이하 QEM) 라고 한다. 구글 애널리틱스 류의 사용성 분석 툴과는 결이 다르다. UI/UX를 개선하기 위해 쓰는게 사용성 분석이라면, QEM은 서비스의 품질을 실시간으로 감시하고 문제가 발생했을때 해결에 도움을 주는 도구이다. 대표적인 QEM 값으로는 X의 소요 시간, Y의 성공률/실패율 등이 있다. 예를 들어 앱의 로딩 시간을 기록하여 사용자가 실제로 경험하는 서비스 속도를 수치화할 수 있다. 그 수치를 근거로 행동을 할 수도 있고 얼마나 개선됐는지도 확인할 수 있다. 또한 핵심적인 서버 API 같은 중요한 작업의 성공/실패를 기록하여 실시간으로 모니터링하면 서비스 운영중 발생하는 변칙 상황을 빨리 파악할 수 있다. 

모니터링 할 때는 도출된 수치가 현상을 관찰하는 목적에 부합하는지 주의해야 한다. 다양한 변수를 고려해서 값을 뽑아내고 분석해야 한다. 특정 네트워크 요청이 실패하는 횟수가 늘었다 하더라도 사용자가 늘어서 실패가 많은 것인지, 아니면 시스템에 문제가 생겨서 실패가 증가한 것인지에 따라 다른 행동을 취해야하기 때문이다. 또한 성능 관련 수치를 분석할 때도 신중하게 값을 봐야한다. 스마트폰의 성능, 지역별 네트워크 품질에 따라 사용자는 우리의 예상과는 전혀 다른 경험을 할 수도 있다. 그래서 의미있는 특징에 따라 모집단을 분할하는게 좋을 수도 있다. 또한 단순 평균 값 뿐 아니라 P50, P90, P95 값 등 여러 가지 통계적으로 유의미한 수치를 함께 봐야 현상을 더 종합적으로 파악하고 그에 맞는 행동을 취할 수 있다.

### 각종 현황판

QEM 같은 실시간 모니터링 외에도 조직에 유용한 현황판을 구축할 수 있다. 현황판은 본래 목적 외에도 개인적으로 성취감과 동기 부여 차원에서 긍정적인 효과가 있었다. 모듈별 코드 커버리지를 표시하는 현황판에 커버리지가 너무 낮으면 빨강색이나 노랑색, 목표치를 달성하면 초록색으로 표시를 했는데 처음에는 빨강색 투성이던 현황판이 점차 초록색으로 물들어갔다. 테스트를 작성하는 일은 신규 기능 개발처럼 결과물을 눈으로 볼 수 없는데, 현황판을 이런식으로 구성하니 테스트 코드 작성 업무도 마치 결과물을 눈으로 볼 수 있게 되는 듯한 효과가 생겼다. 클라이언트 개발자라서 그런지 시각적인 결과물 덕분에 더 열의를 가질수 있던 것 같다. 

다만, 현황판은 수시로 들어가서 보는 용도로 만들면 안된다. 그랩의 전 CTO였던 마크 포터는 *"현황판을 계속 쳐다보고 있지 말아라. 현황판을 언제 들여다 볼지 알려주는건 경보의 역할이다"* 라고 했다. 현황판을 보기 좋게 만드는 것에 그치지 말고, 이상 현상이 생기면 자동으로 알림을 받을 수 있게 만들어서 실수로 중요한 사건을 놓치거나 대응이 늦어지지 않게 하자.

## 3. 업무 문화 개선

프로세스는 조직마다 각양각색이기 때문에 모든 조직에 일률적으로 적용할 수 있는건 없으므로 올해 우리팀이 했던 것 중 효과가 좋다고 생각하는 사례 두 개를 정리했다. 

### QA 팀과 긴밀한 협업

우리 팀은 업무의 20%를 코드 리팩토링 및 구조 개선에 투입하기 때문에 신규 기능이 아닌 기존에 잘 동작하던 기능도 종종 영향을 받는다. 상반기에는 이런 리팩토링 업무에 QA 담당자가 할당되지 않고 개발 팀 내부에서만 진행됐다. 그러니 정보가 잘 공유되지 않아서 QA 팀이 정기적으로 회귀 테스트를 수행할때 어디를 유의해서 봐야하는지 몰라 중대한 회귀 버그가 몇 번 발생했다. 그 후로는 개발 팀과 QA 팀이 기술 과제의 영향 범위와 수동 테스팅의 필요성을 함께 평가하기 시작했고, QA 팀 모르게 기존 부분에 영향을 주는 일을 없애고자 했다. 최근에는 좀 더 자동화하여 개발자가 본인의 MR에 영향 범위를 표기하면, 회귀 테스트가 시작될 때 수정된 부분이 자동으로 수집되어 QA 팀에 보여지도록 하고 있다.

또한 그랩 앱은 [피쳐 플래그(feature flag)](https://en.wikipedia.org/wiki/Feature_toggle) 기법을 도입하고 있어서 기능을 전부 완성하지 않고도 마스터 브랜치에 코드를 병합할 수 있다. 하지만 현실에서는 피쳐 플래그로 관리할 수 없는 개발 업무도 있다. 이럴 때는 어쩔수 없이 피쳐 브랜치에서 개발해야 하는데, 단일 앱에 120명 이상이 기여하고 있다보니 병합이 늦어질수록 코드 충돌의 가능성이 매우 높고, 코드 충돌을 해결하는 도중 새로운 버그가 생길 수 있는 위험도 커진다. 따라서 이런 류의 기능도 QA 팀과 잘 조율해서 개발이 완료되면 최대한 빨리 수동 테스트를 거쳐 신속하게 병합할 수 있도록 프로세스를 갖춰가고 있다.

### 주간 버그 회고 미팅

우리 팀은 매주 한시간 다같이 그 주에 발생한 버그를 돌아보며 버그의 원인이 무엇이고 앞으로는 어떻게 다르게 해야 똑같은 문제가 발생하는걸 원천 차단할 수 있을지 논의한다. 버그를 유발한 사람을 찾거나 책임을 묻는 일은 일어나지 않는다. 누구나 실수할 수 있지만 똑같은 실수가 두 번 이상 발생하는 건 팀의 문제라는 생각에 기반한다. 테스트가 미흡했다는 걸 알게 되면 테스트를 보완한다. 여러 개발자가 동일하게 실수하는 부분이라면 문서를 만들어서 지식을 공유하거나 아키텍처를 수정한다. 새로 도입해야 하는 기술이나 프로세스가 있다면 누군가가 추가적으로 조사해서 후속 조치를 한다. 경험상 이 회고 미팅에서 활발하게 토론이 오고 갔고, 팀원 간에 정보와 지식 교류가 생겨 팀이 다함께 성장한다. 버그가 줄어서 회고 미팅이 짧게 끝날 때는 뿌듯함과 성취감을 느낀다.

## 다중 방어선과 자동화 우선주의

각종 대책들은 서로를 보완한다. 거친 가루는 체로 여러번 걸러야 고운 가루를 얻을 수 있듯이, 어느 방법 하나만으로 모든 버그와 문제를 잡아낼 순 없기 때문에 여러 종류의 대책을 다중으로 배치해야 한다. 그렇기 때문에 각 방법들이 가지는 장점과 한계까지 제대로 파악해서 빈 구멍을 메꾸는 것이 중요하다. 예를 들어 아무리 테스트 코드를 짰더라도 완벽할 순 없다. 테스트 케이스가 미흡하거나, 코드의 로직 자체가 잘못 작성되어있을 수도 있고, 테스트가 실패한 걸 발견한 개발자가 코드를 임의로 수정해서 통과한 것처럼 만들 수도 있다. 그래서 자동화 테스트도 다른 방법으로 보완해야 한다. 코드 리뷰로 여러 개발자가 수정 사항을 확인하게 함으로써 실수를 찾아낼 수도 있다. 뿐만 아니라 유닛 테스트만으로 코드의 일부분만 독립적으로 테스트하다보면 실제 환경에서 제대로 동작하지 않거나 크래시가 나는 상황이 생기기도 한다. 그래서 좀 더 통합적인 UI 테스트로 이를 보완할 수 있다. 그 뒤로도 QA 팀을 거치는 등, 새로 짠 코드가 배포되기 전 문제를 미리 발견할 수 있게 방어선을 겹겹이 구축한다고 생각하고 접근하자.

새로운 프로세스나 정책을 도입할때는 자동화하는 방안을 최우선적으로 모색해야 한다. 자동화하지 않은 프로세스가 늘어날수록 팀에 족쇄가 채워진다. 개발자가 수동으로 해야했던 단순 반복적인 업무가 어느날 소리소문 없이 사라지는걸 경험해본 사람이 있을 것이다. 그러면 최악의 경우, 예전에 발생했던 버그나 문제가 또 일어나게 된다. 프로세스는 자동화해야 장기적으로 지속 가능하다는 사실을 언제나 되새겨야 한다. 업무 프로세스든 앱의 기능이든 새롭게 추가하는건 쉽지만 무언가를 빼는 건 훨씬 어렵고 누구도 선뜻 나서서 하지 않기 때문에, 새로운 프로세스는 최대한 자동화할 수 있는 방법을 찾고 수동으로 할 수 밖에 없는 업무는 최대한 소극적으로 도입한다.

## 결론적으로,

앱의 안정성을 높이고 꾸준히 유지하기 위해서는 코드 품질부터 시작해서 개발 프로세스, 테스트, 출시, 운영 단계까지 모든 영역에서 개선을 해야만 한다. 팀원들이 문제 의식을 공유하는 것부터 시작이다. 다만 모든걸 한번에 할 수는 없으므로 팀의 상황과 역량, 우선 순위에 따라 단기/중기/장기로 나눠서 계획을 세우고 꾸준히 새로운 시도를 하면서 배워나가자. 앱 안정성을 높이면 궁극적으로 사용자의 만족도가 올라갈 뿐 아니라 개발자도 자신감이 생기고 행복해진다. 