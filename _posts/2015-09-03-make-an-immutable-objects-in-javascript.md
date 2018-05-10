---
layout: post
title: Make immutable objects in javascript
date: 2015-09-03 21:44:33.000000000 +00:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- javascript
tags:
- javascript
author:
  display_name: luka
---
Cool thing I ran into when watching Douglas Crockford's talk on **Javascript: the good parts** was a different way of creating objects, with lot more options than usual way I was doing it. More specifically, I found it interesting to see that we can set certain properties as non-writeable ie. **immutable**.

So what I do usually when creating an object is this:

~~~ js
var obj = {"foo": 1};
// Object {foo: 1}
obj.foo;
// 1
obj.foo = 2;
// 2
obj.foo
// 2
// and we've successfully changed object property
~~~
How about making it non-writeable? Here is the code from Douglas' talk:

~~~ js
var obj = Object.defineProperties(
  Object.create(Object.prototype), {
    "foo" : {
      value: 1,
      writeable: false
    }
  }
);
// Object {foo: 1}
obj.foo
// 1
obj.foo = 2
// 2
obj.foo
// 1
~~~

Besides "writeable" we can change enumerable (could this object/property be a part of for..in loop) and configurable (could this object/property be deleted)

Fun stuff..

Cheers..