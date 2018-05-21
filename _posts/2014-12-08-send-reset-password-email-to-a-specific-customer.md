---
layout: post
title: Send 'reset password' email to a specific customer
date: 2014-12-08 22:22:49.000000000 +00:00
categories:
- Magento
tags:
- Customers
- email
- Magento
- Reset password
author: luka
---
Let's say you want your customer to change his password. Nice way to do it is to send him a 'reset password' email. Here is the snippet:

```php
<?php

    require 'app/Mage.php';

    Mage::app();

    $customer = Mage::getModel('customer/customer')->load(63);

    if ($customer->getId()) {
        try {
            $newResetPasswordLinkToken = Mage::helper('customer')->generateResetPasswordLinkToken();
            $customer->changeResetPasswordLinkToken($newResetPasswordLinkToken);
            $customer->sendPasswordResetConfirmationEmail();
            echo "CHECK YOUR EMAIL";
        } catch (Exception $exception) {
            echo "FAILED";
            return;
        }
    }
```

He will receive an email pointing him to the form where the new password should be entered. This is actually the same as going to the website and clicking the 'forgot password?' link and then entering the email to reset it.

I hope someone finds it useful.
