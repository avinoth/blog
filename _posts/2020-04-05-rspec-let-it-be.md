---
layout: post
title: RSpec let it be
date: 2020-04-05 16:51 +0530
categories:
- programming
tags:
- rails
- ruby
- rspec
---
This is going to be a small post about an excellent tool I discovered recently. I was refactoring a large RSpect suite and trying to speed up the execution time. I wanted a reliable benchmark tool to determine where the time is spent the most and which tests are taking most of the time. That's when I came across [test-prof](https://github.com/palkan/test-prof).

Rather than me, the official documentation of test-prof is excellent in explaining all the APIs it supports. [Here it is](https://test-prof.evilmartians.io/).

Out of all the goodies the tool has, one that has found its way to most of the spec suite was the `let_it_be` helper it provides. Its a simple helper that avoids the usecases where you used to use instance variables in a before call to setup non-changing test data.

```
before do
  @draft = create(:post)
  @published = create(:post, draft: false)
 end
 ```

 becomes

 ```
 let_it_be(:draft) { create(:post) }
 let_it_be(:published) { create(:post, draft: false) }
 ```

 By this you can still with the goodies of proper `let` usage but at the same stop abusing instance variables. This and much more are documented [here](https://test-prof.evilmartians.io/#/let_it_be).
