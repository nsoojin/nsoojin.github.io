---
layout: post
title: "The Advantages of Writing a Test Code"
categories: []
tags:
- tests
type: post
published: true
meta: {}
---

I uploaded a full request after modifying the code, but when I saw that the test failed, I searched for the parts that I missed and corrected them. That was when I really felt the value of testing. My team measures coverage as KPI. 20% of working hours allotted for technical assignments such as making tests, build time improvements, and modularizations. There is something I felt through the experience of writing and maintaining the test codes.

### It makes you follow a better architecture

A code that is difficult to test is highly likely to have a poor structure. A testable code incorporates 
concerns about dependency. You need to strictly follow the dependency inversion principle as you manage and organize the dependency of the source code. Uncle Bob explains it well through the [dependency rule](https://khalilstemmler.com/wiki/dependency-rule/).

### Dogfooding My Codes

Once you make the test, you can experience the codes you've written as a user.
It's like building a house and decorating the interior and then coming out to check how it looks from the outside.
Checking what the window looks like, how to open the door, and where the entrance is. The window and the entrance are the same as the public API. The interior of the house is the private elements. We use the public API to check object actions in a test. We try generating an object ourselves, configuring them, calling a public method, or approaching the properties. By doing that, you can check the usability of the codes that you've written.
If the API is sloppy, it's difficult and painful to make a test. You may face situations where you may get thoughts like, 'do I have to test this?' or 'why is this case so difficult to test?'. 
Therefore, you have no choice but to pay attention to the usability of the API, and if you pay attention, you can create a simpler and more intuitive API. 
Being simpler has several meanings, but I think the fact that there are a few public methods or properties is especially important. 


### A Simple and Intuitive API Design

Developing a feature → Making a test → Modifying the feature → Modifying the test.
If you keep repeating the above process, it's like growing muscles that can draw a big picture in advance within the designing stage of creating an API. For example, there are cases when the existing tests all need to be discarded and written all over again after some feature modifications. If that's the case, it's highly likely that the design was bad from the beginning. 
Publicly disclose the things that are related to policy, which do not change easily, and the detailed implementations need to be hidden or moved to the outside. 
If the design goes wrong, it would be impossible to make a test, or if it's made, it's going to be a pain to maintain it. 
If this happens repeatedly, we have no choice but to consider the structure, role, and testability of the object. 
It makes you think about what to put in this object, what to take out, and how to add dependencies. 
If you're having trouble thinking about how to design a code or judging which pattern is better, it seems like a good choice to use the test codes as a standard. 


### Increase the team's ability to create and maintain meaningful tests

Unfortunately, it's not like you can just make a test code one day because you want to. 
There needs to be a code structure and an environment where you can make a test. Therefore, you need to introduce and maintain an architecture that can create a test. It's too difficult to create a test on Apple's MVC. There is a problem where a user action event cannot occur without creating a UI. Also, testing is difficult in ViewContoller because the lifecycle-related method is complex and there are too many of them. It's also not simple to inject dependency. 
So architectures like MVVM, VIPER, RIBS, etc. were created. However, introducing architecture is not the end. 
Architectures are not silver bullets, so they constantly change depending on the problems that need to be solved and the situations the team is placed in. 
Therefore, technical leadership that maintains a structure where testing is possible over time, and testing is not difficult but interesting is important.
Also, teamwork which builds an environment that doesn't make testing feel difficult and maintains the level of productivity, and introduces or makes appropriate tools is important.
