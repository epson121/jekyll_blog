---
layout: post
title: Get cached product image url in Magento 2
date: 2020-06-05 18:00:00.000000000 +01:00
author: luka
categories:
- magento
- php
---

Use `\Magento\Catalog\Model\Product\Image\UrlBuilder`, check out an example below.

e.g.

```php

    
    /**
    * Add dependecy to the constructor
    */
    public function __construct(
        UrlBuilder $imageUrlBuilder
    ) {
        $this->imageUrlBuilder = $imageUrlBuilder;
    }

    public function getImageUrl($path)
    {
        $this->imageUrlBuilder->getUrl($path, 'product_page_image_small');
    }
```

List of possible `$imageDisplayArea` values (second parameter) can be found in theme's `view.xml` file. Some of the examples include:

```
product_page_image_small
product_page_image_medium
product_page_image_large
product_swatch_image_small
product_swatch_image_medium
product_swatch_image_large
```

Cheers!