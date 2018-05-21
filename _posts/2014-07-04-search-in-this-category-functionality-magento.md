---
layout: post
title: Search in this category functionality - Magento
date: 2014-07-04 10:13:25.000000000 +00:00
categories:
- Magento
- PHP
tags:
- Magento
- PHP
- search
author: luka
---
Magento search query, when entered in the form.mini.phtml (template/catalogsearch/form.mini.phtml) is doing the search on database in Mage_CatalogSearch_Model_Layer:prepareProductCollection().

In order to do search only in a specific category, you can add your filter in here.

```php
  public function prepareProductCollection($collection)
  {
      $collection
        ->addAttributeToSelect(Mage::getSingleton('catalog/config')->getProductAttributes())
        ->addSearchFilter(Mage::helper('catalogsearch')->getQuery()->getQueryText())
        ->setStore(Mage::app()->getStore())
        ->addMinimalPrice()
        ->addFinalPrice()
        ->addTaxPercents()
        ->addStoreFilter()
        ->addCategoryFilter($categoryFilter)
        ->addUrlRewrite();

      Mage::getSingleton('catalog/product_status')->addVisibleFilterToCollection($collection);
      Mage::getSingleton('catalog/product_visibility')->addVisibleInSearchFilterToCollection($collection);
      return $this;
  }
```

$categoryFilter can be found when submitting the form. For example, you can add this to the form.mini.phtml

```php
  $catalogSearchHelper = $this->helper('catalogsearch');
  $categoryFilter = Mage::helper('catalogsearch')->getSelectedCategory();
  $currentCategory = Mage::registry('current_category');
  if ($currentCategory) {
    $categoryName = $currentCategory->getUrlKey();
  } else if ($categoryFilter) {
    $categoryName = $categoryFilter->getUrlKey();
  } else {
    $categoryName = "";
  }
  ...
```
```php
  <div class="category-search">
    <input type="checkbox" name="category_search" value="<?php echo $categoryName; ?>">
    <label for="category_search">
      <?php echo $this->__(' Search in this category only') ?>
    </label>
  </div>
```

Then create a Helper for CatalogSearch that can get categoryName:

```php
  public function getSelectedCategory()
  {
    $categoryName = Mage::app()->getRequest()->getParam('category_search');
    $category = Mage::getModel('catalog/category')->loadByAttribute('url_key', $categoryName);
    return $category;
  }
```

And lastly, grab the category id and put it in your Layer.php file

```php
  $categoryFilter = Mage::helper('catalogsearch')->getSelectedCategory();
 ```