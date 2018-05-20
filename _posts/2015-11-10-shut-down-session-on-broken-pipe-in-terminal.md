---
layout: post
title: Shut down session on "Broken pipe" in terminal
date: 2015-11-10 21:27:02.000000000 +00:00
type: post
categories:
- server
author:
  email: traxdater12@gmail.com
  display_name: luka
---
So, you're connected to a server via SSH, doing your wizardry and dark magic. Suddenly, **a wild Pidgey appears**. Just kidding.

You left the SSH connection opened, and went to grab a cup of coffee, only to come back and find that you can't do anything with your terminal anymore.
Oh noo.. broken pipe, again?? I need to kill this terminal and start new one, because stupid **Ctrl + C** nor **Ctrl + D** work.

Hold on for a second. There is no need for that kind of blasphemy. There is a simple and easy way to shut down opened session and get back to your already opened terminal.

How, you say?
Use this key combo (in sequence):

`Enter` + `~` + `.`

This will shut down the session and let you go back to your old terminal.

To see other available options try this key combo:

`Enter` + `~` + `?`

Not bad, huh?

Cheers.