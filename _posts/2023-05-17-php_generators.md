---
layout: post
title: PHP Generator
date: 2023-05-17 08:00:00.000000000 +01:00
author: luka
categories:
- PHP
---

Just a regular function, which yields things (but can return them sometimes as well).

Steps:

1. Create a function that `yields` things instead of `returns˙. If you need it to generate numbers, it should yield numbers. If you're traversing a large file, it might yield lines, one by one.

2. Call the function, and store the result in a variable. Good job. This variable is now a `generator`. Every time there's a `yield` in a function, calling it would result in a `Generator` object.

3. Use this generator in a loop, or manually as shown below, to perform some actions. Generators implement `Iterator` interface, which means they have access to all of the `Iterator` [methods](https://www.php.net/manual/en/class.iterator.php).

5. `rewind` can be thought of like this - start a new cycle, and call the code up until the first yield. It allows us to perform checks before trying to yield the value in our loops. `rewind` __is not__ going to revert generator to the initial state. This is not possible with PHP generators.

6. It's possible to return a value in your generator, using the `return` keyword. Result of the return can be fetched with `getReturn()` method. However, calling `getReturn` wen generator has not yet returned a value, would thron an exception.

```php

function nrange($from, $to) {
    if ($from > $to) {
        throw new \Exception("Wrong range.");
    }
    for ($i = $from; $i < $to; $i++) {
        yield $i;
    }

    return 20;
}


$generator = nrange(0, 10);

$generator->rewind();

while ($generator->valid())
{
    $key = $generator->key();
    $value = $generator->current();

    // echo $generator->getReturn(); - will throw an exception as generator has not yet finished (no return value)

    echo $value . " ";
    $generator->next();
}

echo $generator->getReturn();

$generator = nrange(10, 1);
$generator->rewind(); // will throw an exception because of the range being wrong

```

Most common place where you'd use generators is for iteration over objects/values, but instead of hodling all of them in memory, they would be `yielded` one at a time.

> You know I always wanted to pretend that I was an architect.
>
> -- <cite>George C.</cite>