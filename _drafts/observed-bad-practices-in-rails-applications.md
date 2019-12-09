---
layout: post
title: Observed bad practices in Rails applications
---

> Any fool can write code that a computer can understand. Good programmers write code that humans can understand. - Martin Fowler

This is a famous saying used to express the value of writing good, readable code. There are many articles out there that goes into why writing readable code is important and I don't have anything new to add to those excellent write-ups out there. Instead, in this post I'd like to capture some of the bad practices that makes a code illegible.

When you start working on an existing codebase you tend to get a perspective the developer who wrote the code wouldn't have had. Why, the code you write today, you won't know how it's going to look to eyes of the next developer. Over the years I have observed these practices on various Rails applications and they almost always a huge impact on cost of development.

> Though these points are gathered from Rails applications, most of it are applicable to other languages / frameworks as well.

### On Bad practices

So, when it comes to writing tough-to-read code, there are broadly two categories ball might slip. One at writing the code and two at organizing it. Most modern languages / frameworks have tools and provisions that tries to limit the possibility of many bad practices from entering, with strongly typed systems to strict convention for organization, but there are only so much the tools can do.

In a future post I'll talk about some of the tools / process you can adapt to minimize the carnages of the codebase. In this, let's just see them bad-practices.
