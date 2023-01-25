---
layout: post
title: "Siri, deploy an app"
categories: []
tags:
- siri shortcut
- deploy app
- siriops
type: post
published: true
meta: {}
---

<img src="/assets/posts/siri01-en.png" width="400" />
<p style="text-align: left;"><small>Why does Siri's response sound so sad...</small></p>

## Background

I built a Jenkins CI server for the first time early this year, and because of this I became interested in automation, so I've been improving my team's deployment/development process from time to time for a year. Becoming interested is actually a poor expression, and I did not know that I hated meaningless, repetitive tasks this much. I used to think that sometimes doing repetitive tasks that I could do without thinking is good for my mental health. However, I didn't want to do the manual tasks within the development process, so I somehow found a way to automate it and changed it after persuading my teammates. Therefore, we automated most of the repetitive, cumbersome tasks that had to go through many people, and our app is now so simple that even Siri can deploy it. 

## Preparations

Obviously, the app deployment stage needs to be automated. It may vary from team to team, but generally, app deployment requires version up, building, and dSYM processing(uploaded to crash collection tool) in the least, and you need to generate screenshots, and if it's an enterprise app to be deployed within the company, the build should be uploaded to the company infrastructure. 

Fastlane was a great help in deployment automation. An API with high readability can be used to easily create a build script. Since it's an open-source platform, there are various plugins created by several people, so almost nothing is impossible. Especially, xcconfig files can be modified, git tasks can be done easily, and shell scripts also can be added as needed. Once it's introduced, deployment will be possible with just one line of fastlane command.

And you need a build server. There are many cloud-based CI services. There are always start-ups that provide CI services running a promotion booth at developer conferences. There's travis-ci, circleci, bitrise, etc. but this article provides information based on Jenkins, a free CI that requires you to build your own server.

## Connect Siri Shortcuts

Connecting Siri Shortcuts is actually the easiest step. "Advanced preparations" that automate deployment through the CI server take 99% of the time and effort. We worked on automating processes for a whole year and connecting to Siri took only half a day. Ideally, the app will get deployed by clicking the `Build Now` button on the Jenkins job.

If you complete the deployment scenario, go to Jenkins job settings and check `Trigger builds remotely` under Build Trigger, and then assign a token value.

<img src="/assets/posts/siri02.png"/>

From now on, the deployment is done once the POST is sent as http://JENKINS_URL/job/JOB_NAME/build?token=TOKEN_NAME. This way, you need to process Jenkins server authentications in http communications. But if you think this is troublesome work, you can use the [Build Authorization Token Root](https://plugins.jenkins.io/build-token-root) plugin to approach it only with the assigned token above.

If you've installed the Jenkins plugin, the deployment begins once you enter the command below into the terminal.

```
> curl -X POST http://JENKINS_URL/buildByToken/build?job=JOB_NAME&token=TOKEN_NAME
```

Even up to here, deployment became a much less cumbersome task. But ever since Siri Shortcuts has been released, it was my dream to make Siri do the deployment so I went a step further. Run the Siri Shortcuts app on your iPhone and create a shortcut. Put the distribution URL in Get Contents of URL, select POST, and enter the desired command, as you see below, and that's it.

<img src="/assets/posts/siri03-en.png" width="400" />

## Thoughts

Connecting to Siri Shortcuts was very easy but after I've done it, it felt like I got paid off for continuously automating and improving the development process for a year. If the deployment process was like the state it was earlier this year, Siri deployment would have felt too far away or impossible. What presenter Sooyeol Jeon said at the first meeting of Let's Swift at Pangyo, 'do not think you have to write a code that's perfectly injected with dependency from the beginning, but you can make it a little better than yesterday' left a great impression on me. After connecting Siri, it feels like I've gained some experience of what Sooyeol said. 
I was able to come this far by changing the process so it's easier to automate, and by automating it one by one, little by little so it's less troublesome compared to yesterday.

It was also my first time using Siri Shortcut, and it was surprisingly well-made than I imagined it to be. 
There are so many things you can do with a simple interface. Even if statements, loops, variable allocations, receiving user input, putting up alerts are possible and it feels like developing an app. When I told my friend about it, he jokingly said if I should learn programming using Siri Shortcut instead of Scratch, I don't think that's impossible. 
That time, I read an article, No Code is New Programming on GeekNews, and it was about when APIs have really advanced, there will be an interface without codes at the last step, and that is the future of programming. 
I thought Siri Shortcuts may be included in this concept. The article started with deployment automation to praise Siri Shortcuts, but I was greatly inspired by the fact that Siri Shortcuts not only runs apps on my iPhone but also allows me to combine multiple APIs(apps) without any code.

<img src="/assets/posts/siri04-en.png" width="400" />
<p style="text-align: left;"><small>"Okay.." sounded so sad so I changed the response.</small></p>