---
layout: post
title: Passwordless authentication - follow up
date: 2019-04-21 12:53 +0000
categories:
- programming
tags:
- ruby
- rubygems
- passwordless
---

This is a follow-up post to my previous article on [how to implement passwordless authentication in rails](https://www.sitepoint.com/password-less-authentication-in-rails/) in sitepoint. It basically talks about how to implement login with just rails model, controller and a login token.

Recently I received an requirement to build a Password-less authentication system for a client project and while I wanted to do the way I have written in the article, I was looking out for if any viable options are out there since it's been 3 years. And there is one. I stumbled across [passwordless](https://github.com/mikker/passwordless/) by mikker. It's basically a gem that you can add it to your Rails project and get the password-less authentication system enabled.

Barring couple of hiccups I faced (project specific requirements, the PRs were promptly addressed), the gem works neatly as advertised. I've used it for the project and it's running smoothly in production now.

That's why this entry. I wanted to share the gem because it's a nice solution for something I advertise a lot(get rid of those passwords!).
