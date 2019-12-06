---
title: Rubygems Is Down Now
date: 2014-12-02 07:55:03 Z
layout: post
categories:
- programming
tags:
- ruby
- rant
---

At the time of writing this, the gem repoisotry for Ruby (https://rubygems.org) is down due to DDoS.

Waking up to this news is not soothing especially when you have a day off and decided to work on your stuff.

<!-- more -->

I am joining my new job tomorrow and since I have been practicing programming for quite some time I thought of refreshing everything and discovering new libraries by building an basic application in a sophisticated way, trying out all different solutions for a problem and like that. But this outage proved that it can’t be done today.

While looking for any available workarounds it seems these kind of outage has been happened previously too, and people have clamored about how to do workarounds when a entirely dependent repository is down. Apparently now one has quite worked it right until now I guess.

And the funny part is when I try to check the status.rubygems.org, it was down too. Apparantely it should have been hosted in different DNS, but it wasn’t.
Is there any workaround at all?

I tried looking for it but unfortunately there isn’t any authorized mirrors for the repository. But the good news is it only hampers for the new gem, but we can use the gems that were previously installed on the local computer using,

bundle install --local

considering, we would have used many generic gems for our previous projects.

At the least, it got me going rather than watching “Baby eating lemons” videos on youtube.

The latest update as of writing this is,

    We are continuing to investigate all avenues for mitigating the attack.

Let’s hope it gets up soon and some sort of alternative is set in future.

The live status could be viewed in here DNSimple
