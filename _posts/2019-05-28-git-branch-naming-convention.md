---
layout: post
title: Git branch naming convention
date: 2019-05-28 23:58 +0000
categories:
- programming
tags:
- git
---

As you probably know, [git](https://git-scm.com/) is a popular version control software used in projects across the globe. One of the major feature and one that distinguishes it from other tools is it's branching mechanism. Code is usually tracked via commits and grouped by branches in git.

As the branches are lightweight and disposable it's very easy to get cluttered when many people are working on a project or if there are many branches. And so, as anything with software, naming a git branch will itself need a standard convention to be followed in a project to keep it organized and sane. There are many naming formats out there and several project specific ones I've worked with.

<!-- more -->

Over the years I've tried many of those naming formats and they almost always have shortcomings either with discoverability or ease of use or enforcing it as an convention. Sometimes it doesn't scale with many people or with many long living branches. And so my quest began and after several iterations on varying sized projects, I now use a convention which has worked very well on different projects. This post is my best attempt in documenting it.

## The convention

I'll first go over the format and the parts involved and will try to put down some reasonings behind it. The basic structure of the convention is

> username/token/branch-name-by-hyphen

The convention basically works by way of grouping the branch two levels followed by the intention of the branch. We will go over the groupings one by one.

---
> username/\*\*\*\*/\*\*\*\*

The first part is the username. This is usually the unique name used to identify the branch owner. It can be anything as long as it's unique among other developers of the project.

---

> \*\*\*\*/token/\*\*\*\*

The second part is the token. They denote the nature of the branch. I usually use the below list of tokens, but you can customize the way you see fit for the project.

* *feat* - A feature branch
* *hotfix* - Hotfix changes for production issues
* *bugfix* - A bugfix branch
* *chore* - Cleaning up / organizing the code
* *wip* - Branched out for some WIP stuff that has to be pushed

On most cases these tokens would just work. In some cases optional branches like *tech*, *test* branches are used. But, I mostly prefer to represent the nature with one of the above five tokens.

---

> \*\*\*\*/\*\*\*\*/branch-name-by-hyphen

The last part is the actual name of the branch you have chosen. Usually the name will be reflective of the feature name or the intended change. The names are separated by hyphens to make the words look easily distinctive as these are often used in URLs.

## The explanation

Apart from the benefits of quickly identifying a branch's owner or it's nature, it also has few practical benefits when actually working with it. I've documented couple of them here.

#### Wildcard filtering

Since the branch is split by a separator, it's very easy to filter branches by their nature, author or search by name. If I want to get all branches by a user, it's as simple as

```
git branch --list 'vinoth/*'
```

Or to get all the bugfixes worked by a user

```
git branch --list 'vinoth/bugfix/*'
```

This allows you to quickly narrow down the branches and to easily inspect them.

#### Autocomplete

Since these are separated by parts and we saw how easy it is to narrow down the branches, one additional benefit we can leverage from it is by way of autocompleting the branch names.

Switching branches is one of the common things you have to do when working with git and instead of hunting down the branch name each time or going about entering the full names, you can basically autocomplete the git branches by starting with the first recognizable attributes of a branch - it's owner and from there you can drive it down with the token and the name.

Autocompleting this way would simply save you keystrokes and this is a much better way than actually remembering and recognizing from the names of the branches, especially after you accumulate many number of those.

## Conclusion

I would stop selling it here. I believe once you instill this on a project and as the project grows by size / team, you will automatically see it's benefits - either the ones I've documented or the ones I didn't.

At the end of the day, it's better to have a convention than not, and this one (so far I have worked with) does not have any big downsides / splits the opinions and as far as conventions go that itself is a **big +** for adopting it.
