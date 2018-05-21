---
layout: post
title: Add category name to body class - Magento
date: 2014-07-04 11:47:58.000000000 +00:00
categories:
- Magento
- PHP
tags:
- Magento
- PHP
author: luka
---
Sometimes you would like to customize the page depenfing on the category. For example, you would like to change the header picture or change other things.

In order to do this, you should add the next code snippet:

```php
<?php
$class = '';
$currentCategory = Mage::registry('current_category');

if ($currentCategory) {
    $parentCategories = $currentCategory->getParentCategories();
    $topCategory = reset($parentCategories);
    $class .= $topCategory->getUrlKey();
}
?>
```

to the `/template/page/1column.phtml` or `/template/page/2columns-left.phtml` or any other (where you want it).

Next, append the $addClass variable to the body class like this:
```php
<body <?php echo $this->getBodyClass()?' class="'.$this->getBodyClass() . " " . $class . '"':'' ?> >
  ```