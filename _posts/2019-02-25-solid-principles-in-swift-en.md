---
layout: post
title: "Object-oriented Programming Seen Through Swift: Coding Habits to Avoid"
categories: []
tags:
- Objected Oriented Programming
- SOLID
- Swift
- iOS
type: post
thanks: 
- 김형중
- 김찬희
- 김창기님
published: true
meta: {}
---

## Background

[Someone](http://paulgraham.com/hp.html) who majored in computer and painting and was quite successful said that programmers have more in common with painters than with scientists.
Indeed, as I try to do better in programming, it feels more like a sport or art that requires skills built through experience rather than science.

Just as the ability to recognize good paintings and painting well are different, the ability to judge whether a code is good or bad by looking at it and the skills to build good codes, unfortunately, don't come together.
As I see, build, and maintain a lot of codes built by others, it seems like I've been equipped with a sense that can judge codes a little through experience.
But as I start typing on the keyboard, I face frustration. I get a strong feeling that I shouldn't build it this way from instinct. Experience? But I don't have a clear answer to how to build it in a different, better way.
When you take a test as a student, there are times when you choose your answer with confidence, but also times when you eliminate the ones you know are not correct and choose your answer from the remaining options. If you exclude the ones that are clearly not the answer, you can spend more time thinking about the remaining options, and there's a higher probability that you'll choose the right answer. Coming back to programming, I thought if I knew what a code with a wrong pattern or that is not good for maintenance looks like and avoid it, won't I be able to use better codes?

Last year, I joined a study group for functional Swift programming in the company. On the first day, we invited a mentor to teach a class in the company about functional Swift programming to get advice for the direction of our study. There, the mentor said something that became the direction of my programming study for the next whole year.

To see how well functional programming is, you need to compare a well-written functional code with a well-written object-oriented code, but since I can't say I write good object-oriented code it's difficult to compare them. "Oh.. my unconscious thoughts that thought I was doing OOP because that was the only thing I knew how to do and I code every day in the 'OOP style' started to shake from the roots. So I thought I should do well from the basics again, and this is a summary of knowledge I have acquired over the past few months.

## Swift and SOLID

SOLID is the 5 fundamental principles of object-oriented programs and design, named by Robert Martin. Swift is a multi-paradigm language, but Cocoa Touch frameworks such as Foundation, UIKit, etc. are fundamentally based on OOP. Looking at the SOLID principles and iOS in relation to each other, I could understand why the several classes that I'm using were created that way, and how I could apply these principles. In the text, I focused on how to code in order to keep these principles, and what it looks like to violate these rules, along with examples of the iOS framework for each rule it has been created for (or has violated). As I said earlier, the concept is to at least know what a wrong code looks like, and avoid it, because you can't write good code from the beginning. Then I believe you can somehow move toward that direction even if you didn't understand all the difficult things.

However, this text only deals with SOL, excluding the last two principles. There are several reasons for this. Because I did not understand the latter part enough to explain them, and also because it is much more difficult to judge the situations where these principles are violated for the ID part according to the concept of this text. However, it was enough with only SOL to find countless improvements for my codes and I still can't see the end!

## Single Responsibility Principle

The Single Responsibility Principle is a principle that all classes should have only one responsibility. 
Robert Martin defined it as a reason to change responsibility. 
The most significant violation of SRP is the Massive View Controller phenomenon. 
Many different patterns are being created and tried in order to solve the MVC problem, 
but what they have in common is that they try to split up the view controller, which has too many roles, 
and creates multiple classes that have only a single responsibility.

What we can try right now to keep the SRP is to create small classes. 
Small classes are not sufficient conditions of the SRP but they are essential conditions. 
To achieve this goal, I'm coding based on the 10-200 rule as suggested by the mentor.
The 10-200 rule is to write functions within 10 lines, and classes within 200 lines.
But it's difficult to strictly go by this, and this is still considered an ideal.
In reality, if it goes over 200 lines, you alert yourself inside your head 
and try not to make it longer or try refactoring. 
If you actually try the 10-200 rule, it makes you think a lot and makes you uncomfortable.
However, it seems that learning comes from overcoming these inconveniences.

First, to keep the '10 lines for functions' rule, you'd have to abide by the principle that a function needs to have only one task, and then you start to think about the level of abstraction. The level of abstraction means how much information are you willing to expose in that function. 
Let's look at the cooking directions on the back of the Shin Ramyun bag. Boil 550ml of water, and add noodles, soup base, and vegetable mix. 
It says to boil for another 4 minutes and 30 seconds. The 3 steps of the abstraction level are similar. 
If you break down the water boiling step, you can say take a pot, turn on the water purifier, get water, 
put the pot on the stove, turn on the fire, etc. But these are not included in the cooking directions. 
This is because they are expressed in one step(function) where 500ml of water is boiled. 
This way, it makes me think about how far I should divide the task(function) and explain(abstract) it. 

To keep the '200 lines for the class rule, you can't just create and use properties, functions, and methods anywhere you want.
You need to find a reason why they should be there. If you can't find a reason for them to be in that class, it's a violation of the SRP.
The easiest way to judge is to see how many of the properties or methods of self are being used inside the function or the method. 
If there aren't any being used, there is no reason for them to be in that class. 
Or, the less frequent the calls are made for self, it means it's less related to the class, so if you have to refactor or take the class through a diet, this will be the first candidate to be kicked out.

SRP is called the beginning of all patterns. If you code without properly complying with the SRP, it's highly probable that it will not work with any patterns. Classes need to have only one role, it's the very first principle learned in object-oriented programming, but so hard to comply with.

## Open-Closed Principle

The Open-Closed Principle is a principle that should be open to scalability but closed to modifications.
Being open means that you can add features or make modifications, and being closed means that you must not modify the codes using the module line per line when adding a feature.
A typical example of a violation of OCP is repetitive branching statements for a certain type. That means you need to think about it if you're repeatedly using if/switch statements in several places for one enum. 
Because in cases like this, adding a feature is as easy as adding one line of case, but you need to modify every single code that is switching the enum once you add the feature. If it was planned in an exhaustive manner, you can get help from the compiler, but if you added a default statement, this won't be possible.

OCP can be practiced by not using if/switch as much as possible. 
Not eliminating all branching statements(impossible) but for points like an enum that branch a type.
When I first heard about this, I thought it was ridiculous. Isn't the if statement the most basic of programming.?
How can you branch without an if/switch? It was so awkward and frustrating at first, but as I tried it I'm starting to feel how it is helpful in my codes little by little. 
It is also complementary to the aforementioned 10-200 rule. Eliminating branching statements alone can greatly reduce the length of functions and classes.

Let me introduce to you two ways to replace if/switch statements.
First is creating a protocol (or a class), inheriting and using them.
This is a method that directly abides by the OCP. Refer to the contents of [this blog](https://medium.com/swift-fox/refactoring-replace-enum-with-polymorphism-c4803baeba07) for further details. Another simple method is using the dictionary.

But this method does not directly abide by the OCP. Since this has the same weakness as the switch statement which has a default clause, avoid it when it's likely to have frequent changes, and use it on a limited basis when you want to eliminate branching statements.

Original switch statement code
```swift
switch reason {
  case .initializing:
    self.instructionMessage = "Move your phone".localized
  case .excessiveMotion:
    self.instructionMessage = "Slow down".localized
  case .insufficientFeatures:
    self.instructionMessage = "Keep moving your phone".localized
  case .relocalizing:
    self.instructionMessage = "Move your phone".localized
}
```

Code that deleted branching statements using the dictionary
```swift
//dictionary generated in appropriate spots
let trackingStateMessages: [TrackingState.Reason : String] 
                         = [.initializing.        : "Move your phone".localized,
                            .excessiveMotion      : "Slow down".localized,
                            .insufficientFeatures : "Keep moving your phone".localized,
                            .relocalizing         : "Move your phone".localized]

//switch statement replaced대체
self.instructionMessage = trackingStateMessages[reason]
```

## Liskov Subtitution Principle

Lastly, the Liskov Substitution Principle is a principle where changing the upper type (superclass) to a lower type (subclass) instance should not harm the program's action. The definition is difficult but the way to follow it is surprisingly simple. The child should not restrict the action of its parents.
A typical violation would be a square class created by inheriting a rectangle. 
Since a square has the same width and height, it can only create the desired action only by limiting the action of the rectangle parent class which can freely change its width and height. This is a violation of the LSP.
To abide by the LSP, the rectangle can inherit a square, or make the width and height as a let. 
(If the action of changing the value is removed, there is no action to limit)

An LSP violation case can be also found within the iOS framework.


```swift
var label = UILabel(frame: .zero)
var button = UIButton(frame: .zero)
var segmentedControl = UISegmentedControl(frame: .zero)
var textField = UITextField(frame: .zero)
var slider = UISlider(frame: .zero)
var switchButton = UISwitch(frame: .zero)
var activityIndicator = UIActivityIndicatorView(frame: .zero)
var progressView = UIProgressView(frame: .zero)
var stepper = UIStepper(frame: .zero)
var imageView = UIImageView(frame: .zero)

let views: [UIView] = [...] //Save the above views in UIView array

views.forEach { $0.frame.size.height = 30 } //Change the height for the views to 30

let height = views.reduce(0) { $0 + $1.frame.height } 
print(height) //The result?
```

Since the height of the 10 UIView subclasses has been changed to 30 you may expect it to become 300 but the actual result is 272. This is because the intrinsicSize of some views cannot be changed. 
These views (UIProgressView, UISwitch, etc) that limit the action(changing the height) of UIViews(parent type) are LSP violations.

As you're in iOS development, you may have seen the code below and have created it as well.
If a function that overrides and degenerates the parent's function is created, it's a violation of the LSP.

```swift
required init?(coder aDecoder: NSCoder) {
  fatalError("init(coder:) has not been implemented")
}
```

It's difficult to program without ever violating the LSP.
But if you violate the LSP in too many places, there will be a problem. Code can't be based on the upper class, and if this happens, the inheritance will be meaningless and the OCP won't be followed as well.
For example, UITableView is created based on the UITableViewCell, so if the custom cell isn't following the LSP, the table view won't work properly as well.
Also, the reason for creating a protocol is to write codes based on an abstract interface, but if the inherited type degenerates the protocol's method, the codes written based on the protocol will inevitably break one after another.

Inheritance is an important part of object-oriented programming and is useful if applied well, but could cause problems if wrong inheritances are created.
On the other hand, convenience and simplicity may be obtained by violating the LSP.
In conclusion, it is inefficient to follow the LSP everywhere, but it harms the stability if the LSP is violated too often because of unexpected consequences. 
Therefore, know whether it is violating the LSP or not when creating an inheritance.

## Conclusion

The purpose of this text is not to perfectly understand the SOLID principles. 
In addition, trying to write codes that adhere to all principles can be seen as inefficient. 
For example, it's very inconvenient if you try to create everything according to the OCP.

If you need to create protocols and inherit every time to avoid using branching statements, it only increases unnecessary complexity and increases development time. Therefore, a proper trade-off is needed. 
Among the five principles, the SRP and the OCP is understandable, but the hardest to adhere to and need constant hard work. 

Just like eliminating the wrong answer when you're not sure of the correct one, figure out the habit which violates the above principles, and eliminate it to improve.

Additional references (coding example): [OOD Principles in Swift](https://github.com/ochococo/OOD-Principles-In-Swift), [Design Patterns in Swift](https://github.com/ochococo/Design-Patterns-In-Swift)
