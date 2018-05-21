---
layout: post
title: Show products on transactional emails in magento
date: 2014-08-25 12:06:42.000000000 +00:00
categories:
- Magento
- PHP
tags:
- Magento
- PHP
author: luka
---
One of the requirements from clients will likely be to change transactional emails. Whether they want to add some styling or add new informations, you will have to change the default emails. Sometimes the requirement involves showing products on emails, to increase marketing and in extend revenue.

As you all know, we can add blocks to transactional emails. This articles shows how to add related products to order email. In order to add custom block to transactional email, add this line in the email template:

{% raw %}
    {{block type='core/template' area='frontend' template='custom/block/path/test.phtml' order=$order}}
{% endraw %}
We've used default magento template type. Set the area to frontend, named our template and sent an order object as parameter. Of course, we have to create out test.phtml.

```php
<?php
    // initialize needed variables
    $_order = $this->getOrder();
    $_items = $_order->getAllItems();
    $categories = array();
    $relatedProducts = array();
    $storeId = NULL;
    ?>
    <?php foreach ($_items as $item): ?>
        <?php $itemId = $item->getProductId(); ?>
        <?php $_product = Mage::getModel('catalog/product')->load($item->getProductId()); ?>
        
        <!-- $cats = $_product->getCategoryIds(); -->
        <?php $relatedProductIds = $_product->getRelatedProductIds(); ?>

        <!-- add related ids to array -->
        <?php foreach ($relatedProductIds as $id): ?>
            <?php $relatedProducts[] = $id; ?>
        <?php endforeach; ?>

        <!-- related could be already purchased - remove it -->
        <?php if(($key = array_search($itemId, $relatedProducts)) !== false): ?>
            <?php unset($relatedProducts[$key]); ?>
        <?php endif; ?>

        <!-- set store id -->
        <?php if ($storeId == NULL): ?>
            <?php $storeId = $item->getStoreId(); ?>
        <?php endif; ?>

    <?php endforeach; ?>
```

Get all the products from order object. After that grab all the related products from ordered products, and that's it. Depending on how many products you want to show, place another foreach loop and count until that number is reached. Here is an example:
```php
<!-- get three related products (after the shuffling) -->
<?php if (count($relatedProducts) >= 3): ?>
    <?php shuffle($relatedProducts); ?>

    <div class="ed-section">
        <table cellpadding="0" cellspacing="0" border="0" width="650px" align="center" style="padding:30px;">
        <?php for ($i=0; $i < 3; $i++): ?>
            <?php $relatedProductId = $relatedProducts[$i]; ?>
            <td >
                <table >
                    <tbody>
                    <tr>
                        <td >
                            <div >
                                <table >
                                    <tbody>
                                        <tr>
                                            <td >
                                                <?php $thumbnail = Mage::getResourceModel('catalog/product')->getAttributeRawValue($relatedProductId, 'thumbnail', $storeId); ?>
                                                <?php echo "<img src='" . Mage::getModel('catalog/product_media_config')->getMediaUrl($thumbnail) . "' alt='" . $name . "'/>"; ?>
                                            </td>
                                        </tr>
                                        <tr>
                                            <td>
                                                <div>
                                                    <span>
                                                        <?php $name = Mage::getResourceModel('catalog/product')->getAttributeRawValue($relatedProductId, 'name', $storeId); ?>
                                                        <b><?php echo $name;?></b>
                                                    </span>
                                                </div>
                                                <div>
                                                    <?php $this->__('SKU'); ?>
                                                    <?php echo Mage::getResourceModel('catalog/product')->getAttributeRawValue($relatedProductId, 'sku', $storeId); ?>
                                                </div>
                                                <div>
                                                    <span>
                                                    <?php echo Mage::getResourceModel('catalog/product')->getAttributeRawValue($relatedProductId, 'price', $storeId); ?>
                                                    </span>
                                                </div>
                                            </td>
                                        </tr>
                                    </tbody>
                                </table>
                            </div>
                        </td>
                    </tr>
                    </tbody>
                </table>
            </td>
        <?php endfor; ?>
        </table>
    </div>
<?php endif; ?>
```

Save the template, clear the cache and that should be it. When you receive the ordered email now it will have related products and their attributes (name, sku, price etc.). Of course, you should add your own styling to this and change it according to your needs.

Cheers.