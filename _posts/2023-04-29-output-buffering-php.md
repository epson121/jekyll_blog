---
layout: post
title: Output buffering
date: 2023-04-29 10:00:00.000000000 +01:00
author: luka
categories:
- PHP
---

One of those weird function calls we see when reading internal code for majority of frameworks. `ob_start()` and `ob_end_flush()` may have caused some confusion, but no more. The thing is fairly straightforward.

When we write something out with in PHP, using `echo` or `print`, we're creating output. It can be displayed in a console, or a web page. Buffering an output means storing it temporarily in a memory buffer, so it can be output later or, or even discarded or stored in a variable for further processing.

Output buffering is enabled by default in php.ini configuration using the `output_buffering` directive. It is enabled by default for webserver configuration (mod_php, fpm), and disabled for php-cli environments.

We can test the behaviour in a simple script run in our console:

```php
echo "Here's some output.\n";
echo "Now I'll wait 5 seconds.\n";
sleep(5);
echo "Sending the final piece.\n";
```

This will behave as usual - it'll give us output as soon as it is created and printed in our script. Last piece will be slowed down during sleep call, but eventually, it will be output as well.

However, if we decide to store all of the output inside a buffer, instead of outputing it, behaviour changes.

```php
ob_start();

echo "Output buffering has started.\n";
echo "Nothing has been sent to output yet.\n";
sleep(5);
echo "Sending an output.\n";

ob_end_flush();
```

Our script will store all of the output inside of a buffer, and send it out only when we explicitly say so, or the script ends. This is done with `ob_start()` which basically says - everything that's going to be printed from this point on, needs to be stored in a buffer. So it stores all of the `echo` command outputs, and prints it out only when we call `ob_end_flush()`. PHP documentation has a very good explanation of what this function does, but it basically stops the output buffering, and sends the content out to be printed.

We can even decide to store the output in a variable, to perform some additional changes, if necessary. Like this:

```php
ob_start();

echo "Output buffering has started.\n";
echo "Nothing has been sent to output yet.\n";
sleep(5);
echo "Sending an output.\n";

$output = ob_get_contents();
ob_clean();
ob_end_flush();
```

This script provides no output. We've stored the output in a `$output` variable, cleaned and stopped output buffer via `ob_clean()` and `ob_end_flush()`, so there's nothing to be flushed or sent to the output anymore. However, output is not lost, and it still be echoed out from our variable.

Since we've stopped the output buffering with `ob_end_flush()`, `echo` should work as usual.

```php
ob_start();

echo "Output buffering has started.\n";
echo "Nothing has been sent to output yet.\n";
sleep(5);
echo "Sending an output.\n";

$output = ob_get_contents();
ob_clean();
ob_end_flush();

echo $output;
```

There's the possibility of stacking multiple calls to `ob_start()`, with or without stopping the previous one- i.e. calls can be stacked. What needs to be taken care of is having the appropriate number of stops (by calling `ob_end_flush()`).

So, why is all of this useful? As mentioned in the introduction, a lot of frameworks work this way - like Magento, or Symfony. When it's time to prepare a response - usually by rendering a template, they'd start the output buffer, get the content into a variable and stop and clean the buffer. This way, we can have the whole output in a single variable, and it can be sent out to various other places in the application for additional changes.

`ob_*` functions are not the type of stuff we encounter every day. I've seen them here and there, usually when debugging, and have always been unsure of what they do. They looked very weird, and the name "output_buffering", even though it's very simple when I think about it now, confused me. I remember reading about them probably a dozen times, and forgetting soon after. Hopefully this post makes them stick in my memory, at least for a while.


> If You Ask Me, It's Mighty Suspicious. I'm Gonna Call The Police. Right After I Flush Some Things.
>
> -- <cite>Hermes Conrad</cite>

