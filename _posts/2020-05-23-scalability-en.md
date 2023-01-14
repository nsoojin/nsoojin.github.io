---
layout: post
title: "What is scalability for a mobile developer"
categories: []
tags:
- scalability
- mobile dev
type: post
published: true
meta: {}
---


> Copied from my [Facebook post](https://www.facebook.com/100002633450536/posts/3245101652254251/).

I've had thoughts about what scalability means to a mobile developer for a long time.
I started my career at Line because I wanted to experience dealing with thousands of global users.
But actually, the traffic of thousands of people was something that a server developer had to deal with. I was a little disappointed.

A server developer's skill is measured by how much traffic one has handled,
but for a mobile developer, I felt like there was not much difference in skills or code
in whether one develops an app used by 100 people or 1 million people.
So I wondered what it means to create a scalable code or system in a mobile app.

A developer's skill is concentrated in the section where bottlenecks occur.
Because users get crowded in one place at the same time, the server gets loaded,
and the skill that can solve this load is valuable. But an app does not get loaded because there are a lot of users.
This is because the app gets delivered to the user's doorstep, not the users getting crowded in an app.👇

<img src="/assets/posts/scalability-0.jpeg" />

Then where is the bottleneck that needs to be handled by mobile developers?
Ironically, I thought the bottlenecks in a mobile app occur not because of the users, but the developers.
I thought a point where a lot of developers push their code into an app
that gets deployed as a single program could be seen as a bottleneck.👇

<img src="/assets/posts/scalability-1.jpeg" />

Just as the server's structure and codes should be different when there are 1,000 users and millions of users, an app's development environment and code structure should be completely different when there are only 3 developers and 100 developers. If not, the consequences of the bottleneck pass on to the developer and the users as well. The first app startup time increases and the app size keeps increasing. Not only that, more crashes happen, decreasing user satisfaction, or you may have to go through hotfixes the whole time due to the side effects that keep showing up. Also, build time increases, decreasing the developer's productivity and quality of life due to stress. On top of that, when 100 people are pushing on the master branch, even QA, dealing with bugs, and deploying apps aren't simple tasks. This is because someone is pushing in codes with developed features while someone's cherry-picking while fixing bugs. It doesn't have to go up to 100 people. Even if there are more than just 10 developers, problems like these start to show up. 

So I think I recently found a clue to my curiosity. I thought what scalability to a mobile developer means is that as a company grows and the mobile team grows, it's keeping both the user experience and the developer experience to not deteriorate, and keep deploying the app quickly with confidence.
