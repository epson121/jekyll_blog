---
layout: post
title: Block caching in Magento
date: 2014-09-16 12:43:51.000000000 +00:00
categories:
- Magento
- PHP
tags:
- block
- caching
- Magento
- PHP
author: luka
---
Everyone knows what a caching is, but to put it simply, it is a mechanism to store (save) data that is frequently accessed but rarely changed in order to have a faster response time.

So, for example, if we have a block with featured products on our magento homepage, these products should be cached. Without cache, these products would have to be loaded every time the homepage is accessed. Since this is a homepage, it is expected that it will receive a lot of hits, and if things are not properly cached, a lot of time will be wasted on re evaluating the same queries and drawing the same blocks over and over.

Enter block caching. Magento provides a very simple way to cache the blocks you have. It is as simple as this:

```php
const FEATURED_PRODUCTS_BLOCK_TAG = "FEATURED_PRODUCTS_TAG";

protected function _construct() {
    $this->addData(array(
        'cache_lifetime' => 3600,
        'cache_tags' => array(self::FEATURED_PRODUCTS_BLOCK_TAG),
    ));
}
```

This code should go to your block class. Two thing we are setting up in here are 'cache_lifetime' and 'cache_tags'.
Cache lifetime is a period (in seconds) of time for which your block will be cached. After the expiry of this time, first request that comes along will not be served from cache, instead it will go over the longer procedure and that result will then be cached for the next period.
Cache tags are a special feature. They are optional to set up, but might come in handy sometimes. For example, if we want to clear some of the blocks cached when some situation arises, we can refer to it via tag. Easy way to clean cache by tags is the following:

```php
$cache = Mage::app()->getCache();
$cache->clean(array('TAG 1', 'TAG 2' ...));
```

There is one other thing we can do regarding the caching of the blocks. Imagine the following situation:
You have a website with 2 stores: one for the US and one for the UK. You have the same featured products on both the stores. Here is a problem: when you switch the stores by clicking on the country flag or something, blocks will have to be re-evaluated the first time you do this, and saved to cache. This is not a big issue, since it will only happen once, but we can go around it. We can use 'cache_key'.
By adding a 'cache_key' like this:

```php
'cache_key' => 'SOME_CACHE_KEY_THAT HAS TO BE UNIQUE'
```

we make sure that the same block is cached across all the stores (even if you have 10 of them). But keep in mind that this key has to be unique and the cached block is not able to have any modifications (messages on different language, translation, etc.).

Hope this was helpful. Cheers.