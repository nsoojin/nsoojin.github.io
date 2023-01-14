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

## Code Review and Pair Programming
> "Code review is a core feature of an iOS team. (...) Pair programming is often used."

Minseok Lee, dean of the Innovation Academy, said that 'a code made without review is like a private loan.' I couldn't agree more with it. There's a term called technical debt in the industry, and it means that a code created by a single developer generates a lot of technical debt like high-interest debt. It's really unhealthy to identify yourself with your code and try to protect it from change due to excessive attachment. A code should be treated with the idea that it is a common asset owned by the whole team. If you do a code review, techniques are shared and the whole team grows together. As you're in development, sometimes you might feel proud and think, 'this is really perfect. I don't think there's going to be a particular opinion from the review as well.' But still, your colleagues will find room for improvement, suggest new ideas, and you can learn new techniques. If you don't look at each other's codes and point out room for improvement, you can't grow as an individual or as a team.

Pair programming is a higher version of code review. It's more effective for two people with asymmetric skills than for two people with a similar level of skills to do it. Google considers codes created out of pair programming as [approved for a code review](https://google.github.io/eng-practices/review/). I can't call this pair programming, but I've [mentioned before](https://soojin.ro/blog/copycat-en) in the previous article an experience when I was a rookie, my senior made me sit beside him and see his code and debug, and that helped me greatly and I still remember it until now. Recently, I engaged in pair programming with a rookie developer with me as the teacher. His response was good and it was a good opportunity for me to organize my thoughts and create a more solid design. Pair programming with a senior developer is a very valuable time for a junior developer. You can't buy this opportunity to learn the techniques and know-how from someone that's better than you and ask questions as much as you want one-on-one.

## A good environment needs to have only a few simple, repetitive tasks, and a lot of time to focus only on development.

To do so, repetitive tasks need to be automated. In addition, an individual, as well as the whole team, can grow together if a code review culture is established.
I hope the above standards and examples can help junior developers who are worried about which company or team to go to, or how to improve their surroundings to grow further.
