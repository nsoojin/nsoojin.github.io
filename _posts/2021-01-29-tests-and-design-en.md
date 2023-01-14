---
layout: post
title: "The relationship between testing and good design, and the effect of bad design"
categories: []
tags:
- tests
- design
type: post
published: true
meta: {}
---

> This is a text written after reading about [TDD is not a design methodology](https://gyuwon.github.io/blog/2019/03/03/tdd-is-not-a-design-methodology.html).

I haven't tried TDD but it's similar to what I felt while intensely adding unit tests and refactoring legacy codes so they're able to undergo testing for a year. 

In summary,

- Testable codes avoid strong coupling and allow for the dependency inversion principle. 
- The single responsibility principle and interface segregation principle need to be taken care of on their own. 

Last year, it started by making the codes testable. The basic structure of RIBs is divided into fine parts, and because each element adheres to the single responsibility principle, important logic is testable to some extent.
Most architectures are similar except for the MVC. I think that's why the developer's design ability is important in only the 15-20% of codes that are out of the architecture in a mobile development environment. 

But the 15-20% of code is very important. Similar to an app's design or features, codes are templatized and easily replicated and spread. If there's enough codebase, it's rare to do a complete redesign, but you get to find an existing code that serves a similar purpose. Most people simply copy and paste the existing code. 

We've seen a poorly designed common module having a bad effect on dozens of modules that depend on it as well as dozens of developers. We've also seen a poorly designed structure that was put in by someone spread out. Because an unnecessary dependency is added as a bonus, we are not able to adhere to the interface segregation principle and the build time is increased as well. Also, an untestable structure created by someone might spread due to other developers taking it and using it without much thought. Not only did the code become bad, but a meaningless code review was forced, wasting time for the code author and the reviewer.

Therefore, when creating a common module or introducing a new structure, it should be designed especially with responsibility considering its long-term effect in the future. 
Google says in the [code review guideline](https://google.github.io/eng-practices/review/reviewer/standard.html), “a developer needs to improve code quality as he/she completes the given tasks. If there is no constant improvement, the quality will never improve.'
