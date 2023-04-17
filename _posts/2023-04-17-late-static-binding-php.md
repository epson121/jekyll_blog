---
layout: post
title: Late static binding in PHP
date: 2023-04-17 14:00:00.000000000 +01:00
author: luka
categories:
- PHP
---

Interesting nugget of PHP behaviour I was not aware of. Check it out.

In *static inheritance*, when calling a parent method that itself calls other statically defined methods using `self::`, those calls reference method  __from parent class__, even though we might have them overridden in our derived class.

Late static binding tries to fix this by introducing a way for those calls to reference method from derived class, if available, by calling them with `static::` (instead of __self__);

Example:

```php
class A {
	public function name() {
		echo __CLASS__;
	}

	public static function self_call() {
		return self::name();
	}

	public static function static_call() {
		return static::name();
	}
}

class B extends A {
}

B::self_call(); // A
B::static_call(); // A
```

As expected, we get `A` both times, since we don't have anything in our B class. Now, if we override `name` method, in our B class, behaviour changes a bit.

```php
class A {
	public static function name() {
		echo __CLASS__;
	}

	public static function self_call() {
		return self::name();
	}

	public static function static_call() {
		return static::name();
	}
}

class B extends A {
    public static function name() {
		echo __CLASS__;
	}
}

B::self_call(); // A
B::static_call(); // B
```

This is where `late static binding` comes to play - using the `static::name()` we're binding the `name` method at runtime, to derived class `B`.


> That was the saltiest thing I ever tasted! And I once ate a big heaping bowl of salt!
>
> -- <cite>Phillip J. Fry</cite>

