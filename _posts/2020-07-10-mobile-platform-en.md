---
layout: post
title: "A mobile development environment where individuals and teams grow"
categories: []
tags:
- mobile platform
- growth
- mobile ci/cd
type: post
published: true
meta: {}
---

I was on Facebook yesterday when I saw a job posting for a company's iOS team. 
Usually, job postings only stand out with a few keywords such as welfare, technology stacks, etc. but the contents of this job posting were very easily read just like a tech blog. Many of the things I wanted to introduce when I was in my old team were already built here. 
I tried introducing these things one by one alone as I persuaded my teammates, so I knew that it would have taken a lot of time, effort, and trial and error to build this mobile development environment. As I was nodding my head and reading the post, I thought, 'what if I read this when I was still a rookie?' I don't think I would have understood what's good or how it'd affect me because I've had no experience and knowledge. Now I know because I've experienced both a development environment like this and when there wasn't any. All these big and small things come together to determine the quality of time I spend at work. There need to be only a few things that cut off the flow during development, as well as simple, repetitive tasks.  
Quality time of work means a lot of time to focus on development and this time connects to a developer's growth.

## 배포 자동화
> "매 커밋마다 모든 코드가 테스트되며, 하루에도 수십 번 구성원들에게 개발 중인 빌드가 공유됩니다."

배포 자동화는 클릭 한번(혹은 0번)으로 앱을 빌드하고 내부 사용자나 외부 스토어까지 바로 전달할 수 있는 환경을 의미한다. 배포는 단순하고 반복적이면서 자잘하게 시간이 오래걸린다. 예전 팀에 처음 갔을때는 반만 자동화되어있었다. 앱을 배포하려면 먼저 앱 버전이나 빌드 넘버를 올려줘야하는데 이걸 누군가가 수동으로 커밋을 하고, PR을 만들고 규칙에 따라 굳이 테스트도 다 통과해야하고, 마지막으로 또다른 개발자가 PR을 승인 해줘야 비로소 버전을 올릴 수 있었다. 프로세스가 이러하다 보니 구성원들에게 앱을 전달하려고 할때마다 무려 개발자 두 명의 업무 흐름이 끊어졌다. 이런 방법에 문제를 느껴서 꾸준히 개선을 해나갔고 결국에는 [시리로 배포](https://soojin.ro/blog/hey-siri-deploy-app)를 할 수 있을 정도로 자동화를 해서 배포에 소요되는 노력과 시간을 엄청 줄였다.

개발에 몰입하려면 **방해받지 않는 연속된 시간**이 정말 중요하다. 특히 개발자에게 30분 두 번은 1시간과 결코 같지 않다. 하루에 수십 번 빌드가 공유된다는건 배포가 완전히 자동화돼있어서 개발자는 업무 중에 배포에 신경쓸 일이 없다는 뜻이고 구성원들도 다른 팀원을 방해할 필요없이 가장 최신의 앱을 설치해볼 수 있다는 뜻이다. CI/CD가 구축되어 있어서 이런 종류의 자잘한 방해 업무가 최대한 없는게 좋은 환경이다. 

## Deployment Automation
> “All codes are tested each commit, and builds under development are being shared to the team members dozens of times in a day.”

Automated deployment means an environment where an app is built with a single(or 0) click and is delivered directly to the internal user or external stores. Deployment is simple, repetitive, and time-consuming.
When I went to the previous time for the first time, only half of it was automated. An app version or a build number was required to deploy an app. But someone had to commit it manually, create a PR, pass all the tests according to the policy, and lastly, another developer had to approve the PR before uploading the version. Since this was the process, whenever I tried to deliver the app to the team members, two developers' workflow was cut off. I felt a problem with this method, so I constantly tried to improve it, and in the end, it was automated enough for [Siri to deploy the app](https://soojin.ro/blog/hey-siri-deploy-app-en), and the time and effort spent for deployment were greatly reduced. 

To get immersed in development, continuous time without interruption is very important. Two sessions of 30 minutes are not the same as one hour. Sharing builds for dozens of times in a day means the deployment is completely automated, so the developer does not have to worry about it at work, and the team members can install the latest version without having to interrupt other teammates. A good environment has CI/CD in place, so interruptions like these are reduced as much as possible.

## Feature Flags
> "Introduced feature flags to create an environment where deployment can be done for users every week"

Feature flags are devices that use Boolean values from the back end that can turn features of an app on and off. 
If there are no feature flags, codes cannot be pushed into the master branch so a feature branch should be managed separately. The larger the feature and the longer it takes to develop, the master and feature branches become further away from each other, and it becomes difficult to merge them together later. I've seen cases where the codes get lost and features break because merge conflicts were wrongly modified.  
After experiencing something like this, merging code into the master branch becomes a serious task, causing anxiety and fear. Working hard in development and finishing tests well, but facing problems in the last step while merging codes will discourage developers. 

If you can develop one feature at a time due to the small number of developers, and you won't have to deploy the app during that time, you may not feel the necessity of this environment. But if a feature flag is built in, several developers can maintain only one master branch as each of them is developing a different feature, branch management is much easier, and actual deployment can also take place without any problems. Even if there are underdeveloped codes, the incomplete features can be hidden from the users using back-end values. This way, developers can pay less attention to branch management or merge conflicts, and focus more on development. 
Another advantage of a feature flag is that it can reduce the impact on users by turning off the feature as a temporary measure when problems are found with the released feature.


## 코드 리뷰와 짝 프로그래밍
> "코드 리뷰는 iOS 팀의 핵심 문화입니다. (중략) 페어 프로그래밍을 종종 활용합니다."

이민석 이노베이션아카데미 학장님은 ['리뷰 없이 만들어진 코드는 사채다'](https://www.facebook.com/100002633450536/posts/2990161257748293/?d=n)라는 말을 하셨다. 정말 공감가는 말이었다. 업계에서 기술 부채라는 용어를 쓰는데, 개발자 한명이 혼자서 만든 코드는 고리대금 악성 사채처럼 기술 부채를 많이 발생시킨다는 뜻이다. 자신이 짠 코드를 자신과 동일시 하거나 지나친 애착을 가지고 변화로부터 보호하려는건 정말 좋지 않다. 코드는 팀원들 모두와 공동명의로 된 자산이라는 생각으로 다뤄야한다. 코드 리뷰를 하면 기술 공유가 되고 팀이 전체적으로 같이 성장한다. 개발하다 보면 가끔씩 자만감에 '이건 진짜 완결성 있게 만든거같다. 리뷰에서도 별다른 의견이 나올만한 부분이 없겠지' 싶은데도 다른 동료의 눈에서 개선점이 발견되고 새로운 아이디어가 생기고 몰랐던 기술을 배울수 있다. 서로의 코드를 보여주지 않고 지적하지 않으면 개인으로나 팀으로나 성장할 수 없다. 

짝 프로그래밍은 코드 리뷰의 상위 호환이다. 개발 실력이 비슷한 두 명이 하는것보다는 비대칭적인 실력을 가진 두 명이 하는게 효과적이다. 구글에서는 짝 프로그래밍을 거쳐 만들어진 코드는 [코드 리뷰 승인을 받은 것으로 간주](https://soojin.ro/review/)한다고 한다. 짝 프로그래밍까지는 아니지만 내가 신입일 때 사수님이 옆자리에 앉혀놓고 코딩을 하고 디버깅을 하는 모습을 보여준 경험에 대해서는 [예전 글](https://soojin.ro/blog/copycat)에서도 언급한 적이 있는데 정말 큰 도움이 됐고 아직까지 기억에 남는다. 얼마 전에는 내가 알려주는 입장에서 신입 개발자와 짝 프로그래밍을 했는데 반응도 좋았고, 나도 생각을 정리하고 설계를 더 탄탄하게 할 수 있는 좋은 계기였다. 시니어 개발자와 하는 짝 프로그래밍은 주니어 개발자에게 정말 값진 시간이다. 나보다 잘하는 사람으로부터 기술과 노하우를 1대1로 전수받고 질문도 마음껏 할 수 있는 기회는 어디가서 돈 주고도 못 산다.

## Code Review and Pair Programming
> "Code review is a core feature of an iOS team. (...) Pair programming is often used."

Minseok Lee, dean of the Innovation Academy, said that 'a code made without review is like a private loan.' I couldn't agree more with it. There's a term called technical debt in the industry, and it means that a code created by a single developer generates a lot of technical debt like high-interest debt. It's really unhealthy to identify yourself with your code and try to protect it from change due to excessive attachment. A code should be treated with the idea that it is a common asset owned by the whole team. If you do a code review, techniques are shared and the whole team grows together. As you're in development, sometimes you might feel proud and think, 'this is really perfect. I don't think there's going to be a particular opinion from the review as well.' But still, your colleagues will find room for improvement, suggest new ideas, and you can learn new techniques. If you don't look at each other's codes and point out room for improvement, you can't grow as an individual or as a team.

Pair programming is a higher version of code review. It's more effective for two people with asymmetric skills than for two people with a similar level of skills to do it. Google considers codes created out of pair programming as [approved for a code review](https://google.github.io/eng-practices/review/). I can't call this pair programming, but I've [mentioned before](https://soojin.ro/blog/copycat-en) in the previous article an experience when I was a rookie, my senior made me sit beside him and see his code and debug, and that helped me greatly and I still remember it until now. Recently, I engaged in pair programming with a rookie developer with me as the teacher. His response was good and it was a good opportunity for me to organize my thoughts and create a more solid design. Pair programming with a senior developer is a very valuable time for a junior developer. You can't buy this opportunity to learn the techniques and know-how from someone that's better than you and ask questions as much as you want one-on-one.

## A good environment needs to have only a few simple, repetitive tasks, and a lot of time to focus only on development.

To do so, repetitive tasks need to be automated. In addition, an individual, as well as the whole team, can grow together if a code review culture is established.
I hope the above standards and examples can help junior developers who are worried about which company or team to go to, or how to improve their surroundings to grow further.
