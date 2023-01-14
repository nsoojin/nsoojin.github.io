---
layout: post
title: "Loose Coupling of a Mobile App"
categories: []
tags:
- loose coupling
- interface
- dependency injection
type: post
published: true
meta: {}
---

Programming is the task of efficiently creating software that works properly,
and to do this well, you need to create code that is easy to maintain. A highly effective method to create a code that's easy to maintain is to loosely couple an object. The Gang of Four said the following in a [design pattern](https://en.wikipedia.org/wiki/Design_Patterns).

```
Program to an interface, not an implementation.
```

We call a coupling relationship which is independent of the implementation and relies on the interface, loose coupling. Loosely coupled code is easy to scale and maintain. It can still be managed as the program grows bigger and more complex.

The software design principle and pattern which allow loosely coupled codes to be written are called dependency injections.
It is an element that cannot be found in a tightly coupled code, so to understand why this additional element is needed, you need to understand the advantages of a loosely coupled code first.

### 1. Late Binding is possible.

The implementation can be replaced without recompiling the code. 
Although not a perfect example, looking from a mobile app perspective, it's similar to the principle where users can experience new features even when the app is not newly deployed when the OS gets updated or the framework provided by Apple/Android gets updated. 

However, mobile app developers cannot relate to this. Because the app runs only in an environment defined and implemented by the developer, virtually there's no need for the implementation to be replaced. For example, when creating an app that needs a database, there's no need for a developer to make an assumption or even consider a case where his or her app is going to run on an environment with a different database other than the one introduced by that developer. Therefore, a late connection cannot be considered an advantage taken by a mobile developer. 
But there are more advantages of loose coupling other than this. 


### 2. Easy to scale and reuse.
An app changes its requirements according to the users and continues to evolve according to the business. 
If the class/module is loosely coupled, it becomes easier to develop a new feature or modify and expand existing ones.
Loosely coupled codes are open to scaling and closed to modifications.
In a loosely coupled code, there is a separate composition root, where objects are assembled. Since actual implementations are created and injected here, it is not necessary for the codes that are using the interface to change.

For example, when there is a payment feature in the app, if the code on the product side relies on the payment-related interface, you don't have to touch the codes from the product side that use the payment features when improving or restructuring the payment flow. You can think of A/B testing in a more general way. When you need to provide the same feature in several ways, you may add or remove a new feature without modifying the code which calls the feature. 

### 3. Parallel development is possible.

As the project and the team grow bigger, many developers will work in parallel in one codebase. 
If the codes are loosely coupled, it's easier for the developers to work on several modules simultaneously.

Grab mobile development team has more than 100 developers divided into less than 10 big TFs(Tech Family) in mobility, food, payment CX, etc. Each team uses features developed by other teams(real-time chat, payment, balance inquiry, reward points, user information/authentication, etc.) and is able to simultaneously develop the super-app thanks to loosely coupled codes.

### 4. Maintenance is easy.
A code that clearly distinguishes the boundaries and roles of classes and modules is easy to maintain.
When developing a new feature, it's easy to figure out where to modify it and recognize the scope of impact.
Debugging is also less difficult in code that is coupled with a class or a module which has only one role. 
This is because if the role and the scope are well-separated, it's possible to narrow down the point that may have caused the bug relatively well.

### 5. Testing is easy.
To be exact, unit testing is easy. To do a unit test, the test target needs to be isolated from its dependency. If the objects are loosely coupled, it's easy to replace them as test targets.

There will be some people who do not appreciate the need for unit testing or the ease of testing for code structures. 
According to the [Jetbrains developer survey](https://www.jetbrains.com/lp/devecosystem-2021/swift-objc/) of 2021, 62% of Swift/Obj-C developers do not make unit tests. 
However, once you experience the benefits of automated testing (with a little exaggeration), you can't go back to the days when you didn't make test codes. 

Unit testing allows for the automation of significant testing tasks. More than 100 developers are creating hundreds of thousands of app codes in a month, it's practically impossible to manually check the actions of the new and existing codes without unit testing. 
When our team was just newly created in Grab, the code coverage was low, but after more than a year of adding tests, the number of regression bugs and critical bugs decreased noticeably as a result.

### Conclusion

According to Uncle Bob, the most powerful weapon given to developers by object-oriented languages is the safe use of polymorphism. Thanks to polymorphism being safe, developers can make all of the implementation details that their system is depending on into replaceable plugins. 

It became possible to make the coupling between modules loose. Furthermore, Uncle Bob questions what is the reason for not designing the system this way when you know you can do it this way. ([From The Future of Programming Languages Lecture](https://youtu.be/ya1xDCCMh7g?t=4728))
