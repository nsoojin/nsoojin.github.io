---
layout: post
title: "Learn Mobile Architecture and Development Experience from a Sourcery Developer"
categories: []
tags:
- mobile architecture
- developer experience
- Krzysztof Zabłocki
type: post
published: true
meta: {}
---

> I recorded a podcast with [Krzysztof Zablocki](https://twitter.com/merowing_), who created [Sourcery](https://github.com/krzysztofzablocki/Sourcery), which is an open-source tool that an iOS developer may have heard about. I was greatly inspired just like last year I've seen him. Krzysztof is a developer who leads the iOS team in New York Times and has a goal that aims to improve the developer experience by creating good app architecture and developer tools. 

## Good architecture is a structure where it's easier to make the right choice than the wrong one.

I got the impression that he is a person who interprets mobile architecture and programming from a very pragmatic perspective and puts it into practice. He says the standard for distinguishing a good architecture should be judged on how happy the developer who uses it is. 
Also, he says good architecture is a structure where it's easier to make the right choice than the wrong one. 
Because humans tend to choose the easy path when they have multiple choices. 
The worst thing is to fight the architecture to create a good code. 

So if you look at what he's created until now, it starts from an unexpectedly simple idea, but increases the productivity of developers who program every day, reduce unnecessary repetition to reduce mistakes, or magically force(?) codes to be made with a better structure.
His work improves the whole team's development and debugging experience through [a simple method](http://merowing.info/2016/07/logging-in-swift/) that does not modify existing codes as much as possible if you look at features like [data snapshots](http://merowing.info/2019/07/straighforward-data-snapshots/) and others which can recover the app's status when the user sent a bug report. or the method where you can hide the singleton object which is in charge of app logging using the protocol extension and carrying out unit testing.

## Sourcery that Even Apple Engineers Copied 

There are better and more popular tools. Sourcery is a tool that automatically generates implementation codes when using protocols such as Equatable or Codable in Swift.
There are parts that are supported in Swift itself now, but the default implementation was not initially supported, so it was created when everything had to be manually implemented. Not long after making this, even Apple engineers started using it internally. Funny thing is, they found out about this because the Apple engineers could not send PRs directly to the open source because of the company policy so they had to use Twitter's private DMs to send patches and request for bug fixes. 

Objective-C Playground Revealed Apple's Lies 

Another surprising story is that when Apple announced Swift Playgrounds in WWDC, they said that Objective-C cannot do something like this. In just 12 hours, [Objective-C Playground](https://github.com/krzysztofzablocki/Playgrounds) was created and Apple was proven wrong.
This project not only supports Objective-C, but also Swift, and it's much faster than Apple's Swift Playground beyond comparison. It's even possible to do hot reloading like Flutter. I learned the keyword 'Code Injection' for the first time.

## iOS Architecture and Testing

Lastly, before he became an iOS developer, he used to make game engines, develop graphics, and do web/back-end development. He says among them the mobile part is the most reluctant to write test codes. 
One of the reasons is that MVC provided by Apple is an extremely bad architecture for testing. 
For testing to work well, there needs to be a class that is in charge of logic that is completely separated from UIKit, but "Apple's MVC" doesn't even have an injection and composition so the opinion is that it's difficult. Then why does Apple use MVC for more than 10 years?
There's a pretty good answer to the question asking whether there would be any changes that would push for a better architecture.

First, mainstream architectures other than MVC(based on the 2017 announcement), there are MVVM and VIPER(or other unidirectional architecture).
MVVM needs to have an FRP-like binding, but this is difficult to make and not very compatible with UIKit right away, so it cannot be a candidate, and VIPER isn't easy to learn.
Apple wants new developers to enter the Apple platform easily, so there's no way they would push difficult architectures like this.
For a broad insight into architecture, you can refer to the popular announcement made a few years ago. [Good iOS Application Architecture: MVVM vs. MVC vs. VIPER](https://academy.realm.io/posts/krzysztof-zablocki-mDevCamp-ios-architecture-mvvm-mvc-viper). Now in 2020, since we now have Combine and SwiftUI, is it easier to get out of MVC?

## A Developer Who Creates a Practical Code and Tools That Can Help You Today 

Since he has experience in freelancing and consulting, he's an expert in architecture and tooling which increases development productivity and code reusability. Also, it was especially impressive that he mentioned the specific amount of money he earned from the tools he created out of need. Since time directly relates to income for freelancers, if you create a tool to reduce time spent on redundant tasks every day, or codes used in a previous project become reusable, they will count as income right away. If Uncle Bob and GoF were people who show theory and ideals, Krzysztof is like a developer who creates practical codes and tools that will help you today based on that theory and design patterns. 
I was especially inspired by the way he thought and constantly created something to spread good architecture and get rid of redundant tasks in debugging and coding as much as possible.  
