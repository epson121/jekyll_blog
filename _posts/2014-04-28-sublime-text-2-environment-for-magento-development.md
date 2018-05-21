---
layout: post
title: Sublime Text 2 environment for Magento development
date: 2014-04-28 17:59:34.000000000 +00:00
categories:
- Magento
- PHP
tags:
- Magento
- PHP
- Sublime text
author: luka
---
Recently I got a new job (my first real one, actually), and it is revolving around PHP, i.e. Magento development. Before my first day at the job, I was wondering what kind of IDE or text editor should I use and what kind of one my future co-workers use. I emailed them and the response was just as I expected it to be: "full fledged IDEs like eclipse, netbeans or phpStorm".  I have had a lot of previous experience with those environments, mostly doing Java programming, and I was aware of all the bloat they bring. They were slow most of the time, they were laggy and pretty much uneasy to work with. Sure, that was the case on my own machine which is not really a "state of the art", and I am sure they perform better on more powerful computers.

Since I did a lot of ruby development (mostly coding challenges and some simple rails applications), I used Sublime Text 2. I didn't reallly want to switch the environment since i liked it very much. It was fast, it was responsive and it had a lot of packages for almost anything. I was wondering if it was possible to do PHP (Magento) development using ST2, so i started investigating. I was looking for best packages that involved PHP, watched a ton of youtube tutorials for ST2 + PHP development etc. It was a wild ride.

2 months into the job (i.e. now), I'm still using ST2 and I can say, it's looking pretty good. I've installed a variety of packages, even built some of my own to help me navigate through Magento structure. My workflow is really fast, ant it's incredibly easy to switch from project to project in no time. I would like to share with you some of the packages i found, that are incredibly useful, especially for PHP and Magento development:

1. CTags - this one is a MUST (especially for Magento). It gives you simple and fast "go to declaration" for classes and methods.
2. GoToDocumentation - if you are not very familiar with PHPs standard library, this one is pretty helpful. :)
3. SublimeLinter-xmllint - Magento uses A LOT of XML. 'Nuff said.
4. SublimeLinter - linter that supports PHP. What more should we ask for?
5. Additional PHP Snippets and PHP Completions Kit - many PHP code snippets (especially effective in .phtml files)
6. Other ones i like (not necessarily PHP related) - AdvancedNewFile, SidebarEnhancements (it works on ST2 too, just copy the folder to your packages), TrailingSpaces

This is pretty much it. I'm also working on my own package that eases the magento development: sublime-magento . It is a fork of a project that is not being developed anymore. I'm not really comfortable talking about the details yet, since I'm not sure what else is going to be added, but currently it has some code snippets for easier Magento module creation as well as some utility functions that allow opening files from path etc.