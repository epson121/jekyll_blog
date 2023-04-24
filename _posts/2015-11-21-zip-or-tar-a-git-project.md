---
layout: post
title: Quickly zip or tar a git project
date: 2015-11-21
categories:
- linux
author: luka
excerpt: "There are times when you need to zip your project, either for backup or whatever.
You can always use tar or zip commands. But if you use git as a version control, there is a very simple way to achieve this."
---

There are times when you need to zip your project, either for backup or whatever.
You can always use tar or zip commands. But if you use git as a version control, there is a very simple way to achieve this.

Say hello to git-archive!

It's simple. Type:

    `git archive -o name.zip HEAD`


when in your project directory.

This will automatically zip your project and save the output (-o) to a file called 'name.zip'.
Zip is not good enough? No problem, rename the output file to, for example, `name.tar`.
