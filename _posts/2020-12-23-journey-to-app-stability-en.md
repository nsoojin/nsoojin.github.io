---
layout: post
title: "An endless journey to app stability"
categories: []
tags:
- tests
- app stability
- regression
type: post
published: true
meta: {}
---

Three or four months ago, a friend of mine who is a CTO of a commerce start-up, consulted with me about his concerns. 
A similar bug has been occurring several times in an app that is operating and an important feature suddenly stops working repeatedly, and he asked me how this can be fundamentally improved.
Fortunately, our team's significant achievement this year was app stability improvement.
After constant efforts to improve codes, infrastructure, and development process, app stability has been steadily improving. 
After listening to the situation of my friend's team, I suggested several methods to implement in the long term.

App stability can be interpreted differently for every person and team, so there are various ways to measure it.
From the user's perspective, the percentage of crash-free sessions and crash-free users can be tracked. These values which affect the users directly are information about the things that happen after the release. But stability values that can be measured before the release are not only very important but also deeply related. Generally, codes are deployed to end users after function development, function testing, and regression testing. 
Regression bugs found during regression testing or after deployment are very critical in terms of app stability. 
 
Unexpected bugs occurring in parts where we didn't think the codes were directly modified primarily means there was a problem with the code. But when it's found after the code review, automated testing, and QA, that means there is a hole within our team's process for bugs to escape, so we need to find them and fill them up for each step.
Since each stage has a different method and purpose of improvement, we need to adapt to measures according to each of them and maintain them in the long term.

## 1. Test and Improve Code Quality

First of all, the most reliable and sustainable solution in the long term is improving the quality of the code at the source of the bug and creating tests. Uncle Bob said, "Math proves something as right, but science proves something as wrong. You can trust science because its test coverage is high. Since the software is science, we can prove that our program is wrong through testing. Therefore, we cannot trust software that has low coverage"([original text](https://twitter.com/unclebobmartin/status/1311295606179102720?s=20)). The way to check if the software I made is working the way I intended is to create a test. In an app development environment, testing and architecture have a close relationship. MVC, which we can say is the default for iOS app architecture, is not very suitable for testing. Various architectures have been created to solve problems like this. If MVC is the current main architecture, set up a mid to long-term plan and carry out refactoring to [another architecture](https://academy.realm.io/posts/krzysztof-zablocki-mDevCamp-ios-architecture-mvvm-mvc-viper/). Good architecture reduces a developer's mistakes and makes it easier to code in the right direction. 

Types of tests are divided based on [speed and the degree of integration](https://martinfowler.com/articles/practical-test-pyramid.html), so introduce them according to the situation and circumstances with priorities. iOS's XCTest framework supports unit tests and UI tests. Generally, unit tests run very fast because they can be run on a modular basis. In addition, Unit tests are divided into [Application Tests and Library Tests](https://soojin.ro/blog/application-library-test-en), so you can choose one as technically appropriate. Also, since Xcode lets you know the code coverage as a result of unit testing, you can easily collect the coverage for each module by reading the xcresult file that is created in DerivedData. On the other hand, UI tests typically take a lot of time since it needs to build the whole app and run it in a simulator, but it can test the features, not only a part of a code, just like a person is using the app, so it has the advantage of being able to check if the important features are really working properly. 

Code review is also one of the ways to increase code quality. [Google's internal guide](https://google.github.io/eng-practices/) is very helpful for doing a good code review.

## 2. Building Infrastructure

### Automated Infrastructure

To continuously develop, deploy, and operate, an automated infrastructure called CI/CD is needed. Most basically, it can automate deployment and testing. Deployment can be done at every commit, at a specific time every day, and whenever the developer wants to as needed. You can also ask [Siri to do it for you](https://soojin.ro/blog/hey-siri-deploy-app-en). Also, if your team has a full request-based development process you can automatically run tests for each full request and check if the existing features are not broken even before the new code is merged into the main branch. Automation becomes the basis of development and all the related processes. As much automation is applied, developers can save their time and effort spent on repetitive tasks, and since automated testing is the primary defense line that can catch regression bugs, make sure to build this infrastructure. 

### Real-time Quality Monitoring

To quickly and accurately respond to problems while operating a service after deploying a new feature to the users, a monitoring base must be equipped. The index which represents service quality felt by the end user is called Quality of Experience Metrics(referred to as QEM). This is different from the usability analysis tool from things like Google Analytics. If usability analysis is used to improve UI/UX, QEM is a tool that monitors the service quality in real-time and helps solve problems when they occur. Typical QEM values would be required time for X, success/fail rate for Y, etc. For example, you can measure the actual service speed experienced by the users by recording the app loading time. You can take action based on these numbers or check how it's improved. Also, you can record the success/failures of important tasks like the core server API and monitor their real-time to quickly find out about anomalies. 

Always take a close look to ensure the resulting figures are in line with the purpose of observing the phenomenon. The values should be drawn and analyzed in consideration of different variables. This is because different actions need to be taken depending on whether the increasing number of failures of a specific network request is due to an increase in users, or a problem within the system. You should also look at the values carefully when analyzing numbers related to performance. Users may experience something completely different from what we expected depending on the smartphone's performance and the network quality by region. It might be better to divide up the population according to their meaningful characteristics. Also, you should look at not only the simple average values, but also the several statistically meaningful values such as P50, P90, and P95, etc. in order to figure out the phenomenon more comprehensively and take action accordingly. 

### Various Status Boards

Useful status boards can be built for the organization other than real-time monitoring like the QEM. Status boards had a positive effect when it comes to a personal sense of accomplishment and motivation other than its original purpose. Red and yellow indicated that the coverage was too low, and green indicated that the target value was achieved on the status board which shows code coverage for each module. The board was red at first, and it gradually changed into green. You can't really see the results of creating tests just like developing a new feature, but making a status board like this has the effect of seeing the results of writing test codes with your eyes. Since I'm a client developer, I was able to gain more enthusiasm thanks to the visual results. 

However, you shouldn't create a status board in order to see it from time to time. Mark Porter, Grab's former CTO said, "Do not stare into the status board. It is the alert's role to let you know when to look at the status board." Do not stop at making the status board look good, but make it send you a notification when an anomaly occurs so you don't miss an important event or respond to it late by mistake.

## 3. Improve Work Culture

Since every organization has a different process, nothing can be uniformly applied to all organizations. So I summarized two cases that I thought were effective among the things our team did this year. 

### Close Cooperation with the QA Team

Since 20% of our team's work goes into code refactoring and structural improvement, existing features, not new ones, that used to work well are sometimes affected. In the first half of the year, a person in charge of QA was not assigned to this refactoring task and it was only done within the development team. Therefore information was not well-shared, so critical bugs appeared several times because the QA team did not know where to pay attention when performing regression tests. After that, the development team and the QA team started to evaluate the scope of impact of technical assignments and the need for manual testing, and they tried to eliminate the tasks that affect the existing parts without the QA team knowing. Recently, the task has been more automated and when a developer marks the scope of impact on his/her MR, the modification is automatically collected once the regression test begins and it's shown to the QA team. 

Also, the Grab app introduces the [feature flag](https://en.wikipedia.org/wiki/Feature_toggle) technique, so it is able to merge codes in the master branch without completing the feature. But there are development tasks that cannot be managed using feature flags. In this case, development needs to be done in the feature branch, but since there are more than 120 people contributing to a single app, there's a high possibility of a code collision as the merging is delayed as well as the risk of new bugs appearing. Therefore, a process is being equipped so features like these can be merged quickly after going through manual testing as quickly as possible when they're completely developed with good coordination with the QA team. 

### Weekly Bug Retrospective Meeting

Our team takes an hour per week to look back at the bugs that occurred that week and discuss the cause of the bug and what to do differently in order to completely prevent the same problem from occurring in the future. We do not try to find the person who caused the bug or hold the person accountable. It's based on the thought that anyone can make mistakes, but the same mistake happening again is the problem of the team. If the testing was not enough, we make the test better. If it's something that several developers make mistakes on, we create a document to share our knowledge or modify the architecture. If there is a new technique or a process that needs to be introduced, someone further investigates it and takes follow-up measures. From experience, an active discussion takes place and information and knowledge are exchanged between teammates in this retrospective meeting, thus the whole team grows together. When the number of bugs is reduced and the retrospective meeting ends short, I feel a sense of satisfaction and accomplishment.

### Multiple Lines of Defense and Automation as Priority

Various measures complement each other. Just as the fine powder can be obtained after sifting coarse powder several times, it's impossible to catch all the bugs and problems with just one method, so several measures need to be placed in multiple layers. Therefore, it's important to thoroughly figure out the advantages and limitations of each method and fill up the empty holes. For example, no matter how many test codes you've written, it cannot be perfect. The test case may be insufficient, the code logic itself may be incorrectly written, or a developer who found a failed test may have arbitrarily modified the code to make it look like it has passed the test. So automated tests should be supplemented in other ways as well. We can find mistakes with code reviews by making several developers check for modifications. Not only that, when we independently test a part of a code using the unit test, situations, where it doesn't work properly in actual service or crashes, might occur. We can supplement it with a more integrated UI test. After that, approach it like you're building layers of defense lines to find problems in advance before the new codes are deployed by methods such as going through the QA team.

When introducing new processes or policies, the first priority should be automation. As there are more processes that are not automated, the team gets shackled. Some of you may have experienced simple repetitive tasks that developers had to do manually disappear one day without making a sound. Then in the worst case, a bug or a problem that occurred previously will happen again. Always remember that a process should be automated to be sustainable in the long run. It's easy to newly add a work process or a feature of an app, but it's much more difficult to remove something and no one wants to do it. So look for ways to automate a new process as much as possible, and be passive about introducing tasks that can only be done manually. 

## In Conclusion

To increase app stability and constantly maintain it, all areas, including code quality, development process, testing, release, and operating need to be improved. It begins with teammates sharing the problem. But since everything can't be done at once, divide it into short-term, medium-term, and long-term according to the team's situation, capability, and priority, and make plans as you learn by continuously trying new things. Increasing app stability, ultimately not only increases user satisfaction but also makes the developers confident and happy. 
