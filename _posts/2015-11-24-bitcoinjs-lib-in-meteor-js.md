---
layout: post
title: bitcoinjs-lib in Meteor.js
date: 2015-11-24 21:28:09.000000000 +00:00
categories:
- Bitcoin
- javascript
author: luka
---
If you want to use bitcoinjs-lib in Meteor js, try out this package: https://atmospherejs.com/ca333/bitcoinjs
add it via:
`meteor add ca333:bitcoinjs`

Before you test it, you should install npm package:

    npm install bitcoinjs-lib

Keep in mind that this package lets you access the library only through server - so, no client side code can use it directly. You must use meteor server methods.

Cheers.