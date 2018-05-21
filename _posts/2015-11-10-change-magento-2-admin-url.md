---
layout: post
title: Change Magento 2 admin url
date: 2015-11-10 21:16:28.000000000 +00:00
categories:
- Magento2
author: luka
---
If you're like me, you probably just fly through the Magento 2 installation, without actually reading anything. Usually,
this is not an issue (especially if you've done it several times), but recently Magento has an addition with creating an
administration url.

What this means is that administration is no more placed under **domain_name/admin**. Instead, it has a random
string attached to it, like **domain_name/admin_random_string**. So if, at the end of installation, you're banging your head
at the table cursing yourself for being so "confident", fear not. There is a simple way to change/find out the real url.
Open the `app/etc/env.php` file, and change the "frontname" key value to **admin**.

That's it. Cheers.
