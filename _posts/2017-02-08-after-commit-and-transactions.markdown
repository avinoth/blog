---
layout: post
title: after_commit callbacks and transactions
date: 2017-02-08 09:54:00 Z
categories:
- programming
tags:
- ruby
- rails

---

Recently I was debugging a code on [Intelllex](https://intelllex.com) API and thought I'd share.

We have a product called Stacks, where users can save links, searches and files to it, which helps them with organizing their legal research. Our stack is Rails on the API side and Angular on the client side.

We use [counter culture](https://github.com/magnusvk/counter_culture) gem to cache the counts of things that are saved to stacks by users and we also have the ability where users can save and move stuff from one stack to multiple stacks.

<!-- more -->

So, we had received this complaint that the count is not being properly saved when items are saved to stack. I was working on it and discovered that the bug is, when users save a single link to more than one stack only on the first stack the count is being updated and rest of them are not. Weird.

When users are trying to save a link to multiple stacks, we first save a single copy (call it master copy) and clone the copy to multiple stacks to avoid I/O to our data source for each link save. All the saves here are done under a single transaction.

Hmm, so the count is updated for the master but for other copies they're not getting updated.

I went to next step to check whether the callbacks are ignored for clones. We have other callbacks on each stack save and they were being triggered for all the saves including the master copy and the clones that we make, so it wasn't being the callbacks are being ignored completely, but only the counter callbacks are ignored.

Then, upon checking the counter_culture code, it uses  `after_commit` callback for count updating actions. So, I tested with a sample `after_commit` callback and saved the links, boom, it wasn't being triggered.

So, it's the `after_commit` callback. But I could not find why it's not being invoked. It's clearly not because of transactions or because of clone alone. Something in between is happening.

For the fix I changed the way the links are clone. Instead of saving each clone under a loop, I resorted to constructing an hash of attributes and saving it using AR's `create` method.

Roughly changed the code from,

```
destinations.each do |destination|
  kopy = master.dup
  kopy.stack = destination
  kopy.save
end
```

to,

```
dups = []
destinations.each do |destination|
  kopy = master.dup
  kopy.stack = destination
  dups << kopy.attributes
end
create(dups)
```

I'm still figuring out why the `after_commit` is being ignored in the first case, and Once I figure out will do a follow up post. If you guys know why this is an issue, please help me by posting your thoughts on the comments.

