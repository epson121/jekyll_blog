---
layout: post
title: Add fee or discount to order/invoice/creditmemo email messages
date: 2014-07-19 17:17:46.000000000 +00:00
categories:
- Magento
- PHP
tags:
- Magento
- PHP
author: luka
---
In the last few days I have been playing with the functionality of adding another fee to the checkout process. While exploring the topic I stumbled upon a lot of good tutorials for doing exactly that. Namely this, and this one.
I read them and followed instructions stated there. With some changes, I managed to recreate the functionality I needed. But, while reading the comments for both articles, I saw the main trouble people had with them. The part about showing the fee or discount in email messages of orders, invoices and credit memos was missing (or not implemented), so here is how i did it.

```xml
<blocks>
    <sales>
        <rewrite>
            <order_totals><!--class name here--></order_totals>
            <order_invoice_totals><!--class name here--></order_invoice_totals>
            <order_creditmemo_totals><!--class name here--></order_creditmemo_totals>
        </rewrite>
    </sales>
</blocks>
```
So, in order to do this, you have to rewrite few of the magento blocks (`Mage_Sales_Block_Order_Totals`, `Mage_Sales_Block_Order_Invoice_Totals` and `Mage_Sales_Block_Order_Creditmemo_Totals`).
If you look at the source for these files, you can see they all have the method named `_initTotals()`. This method is responsible for adding the total values for various things to email, for example subtotal, shipping, discount etc. In order to add your fee to the email message, your `_initTotals()` should look like this (for all your blocks):

```php

/**
* Initialize order totals array
*
* @return Mage_Sales_Block_Order_Totals
*/
protected function _initTotals()
{
    $source = $this->getSource();

    $this->_totals = array();
    $this->_totals['subtotal'] = new Varien_Object(array(
        'code' => 'subtotal',
        'value' => $source->getSubtotal(),
        'label' => $this->__('Subtotal')
    ));

    /**
    * Add shipping
    */
    if (!$source->getIsVirtual() && ((float) $source->getShippingAmount() || $source->getShippingDescription()))
    {
        $this->_totals['shipping'] = new Varien_Object(array(
            'code' => 'shipping',
            'field' => 'shipping_amount',
            'value' => $this->getSource()->getShippingAmount(),
            'label' => $this->__('Shipping')
        ));
    }

    /** THIS IS THE ADDED PART
    * (you should change this according to your naming and db column names)
    * Add handling fee
    */
    if ((float) $this->getSource()->getHandlingFeeAmount()) {
        $this->_totals['handling_fee'] = new Varien_Object(array(
            'code' => 'handling_fee',
            'field' => 'handling_fee_amount',
            'value' => $this->getSource()->getHandlingFeeAmount(),
            'label' => $this->__('Handling fee')
        ));
    }

    /**
    * Add discount
    */
    if (((float)$this->getSource()->getDiscountAmount()) != 0) {
        if ($this->getSource()->getDiscountDescription()) {
            $discountLabel = $this->__('Discount (%s)', $source->getDiscountDescription());
        } else {
            $discountLabel = $this->__('Discount');
        }
    $this->_totals['discount'] = new Varien_Object(array(
        'code' => 'discount',
        'field' => 'discount_amount',
        'value' => $source->getDiscountAmount(),
        'label' => $discountLabel
        ));
    }

    $this->_totals['grand_total'] = new Varien_Object(array(
        'code' => 'grand_total',
        'field' => 'grand_total',
        'strong'=> true,
        'value' => $source->getGrandTotal(),
        'label' => $this->__('Grand Total')
    ));

    /**
    * Base grandtotal
    */
    if ($this->getOrder()->isCurrencyDifferent()) {
        $this->_totals['base_grandtotal'] = new Varien_Object(array(
            'code' => 'base_grandtotal',
            'value' => $this->getOrder()->formatBasePrice($source->getBaseGrandTotal()),
            'label' => $this->__('Grand Total to be Charged'),
            'is_formated' => true,
        ));
    }
    return $this;
}
```

Here we are leaving almost everything as it was before, only we are adding another subtotal (our own) to be shown in messages.

That should do it now. Clear the cache and all that stuff before trying. Cheers.