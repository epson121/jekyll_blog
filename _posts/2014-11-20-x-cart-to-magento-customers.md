---
layout: post
title: 'X-Cart to Magento: Customers'
date: 2014-11-20 20:46:33.000000000 +00:00
categories:
- Magento
- PHP
tags:
- Magento
- XCart
- PHP
author: luka
---
At times there is a need to switch the eCommerce platform you are working on. Reasons for this can be numerous. Your existing platform might be outdated, might be without any future improvements coming up, you might have heard great things about some other platform or simply you are not satisfied with the way your current platform works and behaves. Nevertheless, you've decided to do the switch. It is not an easy task to do because, in almost every case, platforms are not compatible with each other and in order to keep all of the data from previous platform (like your customers, orders, reviews etc.), some changes have to be performed on the data.

Customers can't be easily imported since the database structure is not the same and some changes have to be performed. As you are probably aware, Magento has a way of importing the customers via .csv files. This could be a way for us to import the X-Cart customers. Lets have a look.

X-Cart customer database consists only of 1 (one) table. This table has all the data we need, but the column names are not matvhing the ones magento needs to import a customer. (NOTE the easiest way to see what format is required in Magento is to first do an export of a manually created customer in a .csv file, and use it as a template).

The following <a href="https://gist.github.com/epson121/00a5d1601a2744891894">script</a> will do the translation from X-Cart .csv file to Magento .csv file (we've created an X-Cart csv with PHPMyAdmins export tool). When you run this it will create a Magento-compatible csv file for you to import.
(NOTE: if you are working on a staging or dev environment, disable emails because an email will be sent to each customer upon import - not a good idea to do before the launch).

This works good, but what if we have thousands of customers? Magento import works really slowly in those situations. What can we do to import customers more quickly and efficiently?

Here is <a href="https://gist.github.com/epson121/140ad80d2e3309b5eb3e">another script</a> that does exactly that. There is no need to do the importing manually, all you have to do is place the script into your magento root folder change the name of the .csv file where xCart customers are and the customers will be imported.
Since X-Cart allows its customers to register with the same e-mail address more than once, the script takes care that only first occurence of an email is used (others are rejected). Also, it automatically creates shipping and billing addresses, if they exist and are in the right format.

Scripts are mostly self-explanatory so there should be no issues in understanding them.

Cheers.
