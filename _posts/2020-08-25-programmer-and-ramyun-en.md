---
layout: post
title: "A Developer and Ramyun (Instant Noodles) Recipe"
categories: []
type: post
published: true
meta: {}
---

Let's take a look at the cooking directions at the back of the ramyun bag.

<img src="/assets/posts/ramyun.jpeg" />

```
(1) Boil 550ml of water and vegetable mix. 
(2) Add noodles and soup base, and boil for another 4 minutes.
```

Number 3 is recommendations on how to eat the ramyun so we can say it's actually 2 steps. 
But if you actually think about yourself cooking ramyun, you can see there are actually a lot more steps than this.

```
(1) Take out the pot.
(2) Add 550ml of water and vegetable mix into the pot.
(3) Heat up the pot with water and the ingredients in it.
(4) Wait for the water to boil.
(5) Once the water starts to boil, add noodles and soup base.
(6) Boil for another 4 minutes.
(7) Turn off the heat.
(8) Transfer the ramyun into a bowl.
```

It can be written with more detail. If you look closely, there are several omitted steps within 'heat up the pot'. It may vary depending on equipment so branching it is necessary.

```
(1) If it's an induction, turn on the power and increase the temperature by pressing the button.
(2) If it's a gas stove, open the gas valve, and turn the dial to light it up.
(3) If it's a portable gas stove, shake the gas bottle, insert it, lock it in, and then light up the fire.
```

But no ramyun recipe includes the above directions. That's because they consider the readers to be aware of that much detail, or the ramyun company simply doesn't care. You may think it's too obvious but these kinds of codes are quite common. They may do more than what's expressed in the name of the function, or overly detailed implementations may be revealed. In these cases, there are usually many if and for statements, and the codes are lengthy. 

Imagine, for example, you encountered a function called fetchRecentArticles. You can expect that the tasks performed by this function are as follows: 


```
(1) Load the stored articles as appropriate.
(2) Select and return the ones generated within the last X days.
```

However, as you check inside the function, what if you are checking the local cache within the function, and if there are none, call the server API, import and parse the data, define the key values to load data from storage, and creating different filtering logic according to the types of articles using switch statements?
The core part gets lost inside the lengthy codes. Even if you try to modify the code, you have to find out where to modify it for a long time. You're explaining how to turn on the heat when you only need to know that you have to boil the water for the ramyun.

A similar meaning can be found in [this video](https://youtu.be/3smc7jbUPiE) in which Richard Feynman answered the reporter's question, "Why do magnets push each other away?" Even if the same question is asked, the explanation varies widely depending on who the interviewer is (Someone who majored in physics, an ordinary person, an alien, etc.) or what the interviewer wants to know. It could be a single-phrase answer like, "That's just the way it is," or it could be an explanation with endless detail.


Think about whether the code you've written is revealing only an appropriate level of logic.
You need to examine and correct whether they are only working on a task appropriate for a function's role, or they're lining up all kinds of detailed logic. 
But there is no answer to an 'appropriate level'. It depends on the class or module that the code belongs to.
It may also vary depending on the ability of the reader/writer. A concise code may be a code that is more difficult to understand for some. 
If you refactor a code, the standards may change again. Everything is fluid.
There is no set standard, but the readers can at least read their code once more, 
practice making the functions short, review the code with colleagues and adjust it to an appropriate level.

It's not easy to adhere to the single responsibility principle that an object should have only one responsibility, but let's start with a function unit first. 
If you're now able to make a concise function, apply it at the class level next. The single responsibility principle is the beginning of all patterns or architecture. 
That means, if you aren't able to adhere to the [single responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle), no matter what pattern you use or what architecture you bring in, there's a high possibility that it'll eventually become a spaghetti code as time passes. If you study language grammar as a means of concise expression, you may not get lost. Let's practice making functions shorter and making classes smaller.

### For additional reference

Revisiting Object Oriented Programming with Swift: [Coding Habits to Avoid](https://soojin.ro/blog/solid-principles-in-swift-en)
