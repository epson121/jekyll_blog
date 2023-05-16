---
layout: post
title: PHP Generators
date: 2023-04-22 10:00:00.000000000 +01:00
author: luka
categories:
- PHP
---

Just a regular function, which yields things.


```php

function number_generator() {
    for ($i = 0; $i < 10; $i++) {
        yield $i;
    }
}

$generator = number_generator();
```



> We're in the middle of nowhere, which is the safest part of nowhere.
>
> -- <cite>Phillip J. Fry</cite>