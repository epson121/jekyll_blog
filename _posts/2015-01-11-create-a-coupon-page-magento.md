---
layout: post
title:  "Create a coupon page in Magento"
date:   2015-01-11 18:13:18 +0000
categories:
- Magento
author: luka
excerpt: "Wouldn't it be great if you could show all your coupons to customers on one page? This way they would know where to search for the coupons, and coupons would come from a reliable source - you. Also, you could advertise this page and get more customers to sign up because, well, you offer coupons only for registered customers. ^^
How would we go about doing something like this?"
---
Wouldn't it be great if you could show all your coupons to customers on one page? This way they would know where to search for the coupons, and coupons would come from a reliable source - you. Also, you could advertise this page and get more customers to sign up because, well, you offer coupons only for registered customers. ^^
How would we go about doing something like this?

The first thing that comes to mind is a CMS page. yoursite.com/coupons sounds like a lovely idea. Go and create a CMS page with that URL KEY.
Next question that comes to mind is, how will the coupons be shown? I do not want to edit this page each time I add a coupon or each time a coupon is expired. That is a lot of work, and I simply don't like to do things manually? Fair enough, that's why we'll use Magento blocks and dynamically populate this page. In your CMS page content add the following code:

{% raw %}
 `{{block type="mymodule/couponpage" template="mymodule/couponpage.phtml" }}`
{% endraw %}

What is this? We are going to insert a custom block into our CMS page and render the coupons with it. Our block will reside in our custom module (search for tutorials on how to create your module and register blocks). Also, we'll have a template that will show the coupons (ie. couponpage.phtml).
Let's code.
Our block has to fetch all "shopping cart price rules" that have a Coupon code. Also, rules have to be active. (You could optionally check for expiration date also - if it is smaller than current date, exclude, but beware - if expiration date is empty, it will exclude it always that way).
Here is the code for the block. We simply fetch the colection of rules, and in the result set include only those that have a coupon code, and are active

{% highlight php %}
<?php

class Mymodule_Mymodule_Block_Couponpage extends Mage_Core_Block_Template
{
    public function getCouponData() {
        $rules = Mage::getResourceModel('salesrule/rule_collection')->setOrder('created_at');
        $result = array();
        foreach ($rules as $rule) {
            if ($rule->getIsActive()) {
                $rule = Mage::getModel('salesrule/rule')->load($rule->getId());
                $expirationDate = $rule->getPrimaryCoupon()->getExpirationDate();
                if (!$couponCode = $rule->getCouponCode()) {
                    continue;
                }
                $ruleData = array(
                    'name'          => $rule->getName(),
                    'description'   => $rule->getDescription(),
                    'coupon_code'   => $couponCode,
                    'exp_date'      => date("m/d/Y", strtotime($expirationDate))
                    );

                $result[] = $ruleData;
            }
        }
        return $result;
    }
}
{% endhighlight %}


As for our template, we simply create a file in `yourpackage/yourtheme/mymodule/couponpage.phtml` and paste the following:

{% highlight php %}
<?php $couponData = $this->getCouponData(); ?>

<div class="coupons" style="width:75%;height:100%;">
    <?php if (count($couponData) > 0): ?>
        <?php foreach ($couponData as $data): ?>
            <div class='coupon' style="display:block; float:left; width:25%; height:30%; padding:20px;">

                    <h3 class="coupon-name"><?php echo  $data['name']; ?></h3>
                    <p class="coupon-description"><?php echo  $data['description']; ?></p>
                    <p class="coupon-code"><span><?php echo $this->__('Use coupon code: ') ?></span id="coupon-code"><?php echo  $data['coupon_code']; ?></p>
                    <p class="expiration-date"><?php echo $this->__('Ends ') ?><?php echo  $data['exp_date']; ?></p>
            </div>
         <?php endforeach ?>
    <?php else: ?>
        <div>No coupons available</div>
    <?php endif; ?>
</div>
{% endhighlight %}

And that's it. If you now go to the `yoursite/coupons` you would see a list of coupons your customers can use.