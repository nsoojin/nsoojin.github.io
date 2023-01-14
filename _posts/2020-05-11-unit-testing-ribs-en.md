---
layout: post
title: "Making a uber/RIBs Unit Test"
categories: []
tags:
- uber/ribs
- unit tests
type: post
published: true
meta: {}
---

[RIBs](https://github.com/uber/RIBs) is a mobile architecture framework released as an open source developed by Uber.
RIBs framework separates the complex app status management and business logic into RIBs chunks(referred to as Riblets) and links them in a tree structure. 
The object which composes one unit riblet, and the role of each are as follows. 

<img src="/assets/posts/uber-ribs-diagram.png" />

## RIBs and Object Oriented Programming

Just like the above diagram each RIBs object has a distinctly separate role. (single responsibility)
And the input and output of each object as indicated by an arrow are abstracted with a protocol(dependency inversion), so they can be separated and independently tested using the mocking method.
Also, the parent and child riblets are decoupled into a tree structure, so they can be a child riblet that can be newly created or modified as it minimizes code modification of the parent riblets. (Open-closed Principle)
What I felt after working on the project for about 2 months is that if you use the RIBs structure, the three principles, SRP, OCP, and DIP, which take up a large part in SOLID are semi-forced to keep. 
Of course, they're easy to break as well. No matter what architecture you use, it depends on how the developer writes the codes, and just introducing an architecture doesn't improve my codes.


## What should be tested

The objects that make up the RIBS architecture are very easy to unit test. 
The roles are clear and finely divided, and since the parent and child riblets are loosely coupled, high coverage can be achieved. At least 4+ test classes are needed for each riblet, and if you use Xcode's code generation template, it automatically generates unit test classes as well as boilerplate codes that are cumbersome to make every time. 
When I was first working on the project, I was lost because I did not know what to fill in for these many test classes. 
But as I received code reviews and looked through the previous codes, I found a rule and learned how to test according to the roles and purpose of each class. 


## Router Test: Child Riblet Routing

A Router's test needs to check the action where the child riblet is attached(attachChild) and detached(detachChild) according to business logic. If you look at the Router object description, there's an annotation that says ‘when a router creates a child router, a helper builder must be used.’(link). This means when generating a child Riblet, you shouldn't directly use the xxBuilder class, but abstract it using xxBuildable and get it injected. This way we can check if the router generated the child riblet properly by injecting xxBuildableMock in a test environment. The Router's role is to do routing instead according to the interactor's request, it's desirable that there's no other logic than this. 

## Interactor Test: Various Business Logics

The Interactor is in charge of an app's business logic, so it's more complicated compared to other classes and gives more freedom to developers. Although it's not easy to categorize as other components, there are a few that are being used often. 

An Interactor uses the reactive programming method in order to deliver information to the child interactor(Reference: official document). If you use the reactive stream, the parent and child interactor won't have to engage in direct coupling. It depends on the developer how to implement the stream, but it's usually good to use RxSwift. RIBs architecture also uses Rx, so if you use this, you don't necessarily have to add several reactive libraries. You can check if the interactor is exporting a value properly by mocking the stream in a testing environment. On the other hand, if you have to subscribe to the stream injected by the parent interactor to process tasks, inject a mock stream and check whether it processes well according to the data as you change the values in the test case.

- The interactor receives a user input from view. To check if an appropriate task has been executed according to the user input, directly call the method in the interactor related to the view listener from the test case and check the mocked dependency object to see if the necessary task has been executed. 

- The Interactor calls the router and the presenter(or view) frequently. The view can be attached and detached, and the UI may get updated. So check whether the router and the presenter method are called according to the intent. 
In this case, rather than simply checking if it got called or not, it's better to check exactly how many times it was called. 
You can check how many UI updates are being done unnecessarily, and also check if the view routing is not done in duplication. (Reference: [Official tutorial Mock Object](https://github.com/uber/RIBs/blob/master/ios/tutorials/tutorial2/TicTacToeTests/TicTacToeMocks.swift))

## Builder Test: Generating and Injecting a Concrete Class

The Builder generates a RIBs object to connect the reference points, and creates the concrete classes necessary for dependency injection. Therefore, check the action by using is or as? to see if a correct class type has been generated.

## Presenter Test: View Model Generation Logic

Check if the data model has been converted into a view model. It's recommended that the data model does not use the UIKit class, and keep the UIKit, Core Graphics type, etc. necessary for configuring a view within the view model.  
For example, a data model has a color value as a hex string and the presenter converts it to UIColor. Also, the data model has the date in Date type, and the presenter converts the value into a string using the DateFormatter and saves it into the view model. The conversion logic like this need to be checked. 

## View Test: Rendering Views

For views, it's difficult to do meaningful testing only with codes. If you use a [snapshot test](https://github.com/uber/ios-snapshot-test-case) library, the view is rendered and saved as an image file. You can check the view class(UIView, UIViewController) by rendering the view again and comparing it with the existing image file during testing. If the view changes due to modification in codes, the test case fails, and you can find out that the view is wrong in advance. Since the snapshot test is also a unit test, it gets included in the coverage. By adding snapshot tests, the coverage of the riblet could be increased by approximately 12-15%.
