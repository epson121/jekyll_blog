---
layout: post
title: Add custom attributes to magento SOAP API v1 and v2
date: 2014-09-20 20:46:33.000000000 +00:00
categories:
- Magento
- PHP
tags:
- custom attribute
- Magento
- PHP
- SOAP
author: luka
---
Sometimes a need arises for you to add additional attributes to the standard Magento API responses. While doing this in Mageno
API v1 is fairly easy, as you will see soon, the v2 has some quirks that had to be found out the hard way, unfortunately.
So in order for this to not happen again, here is the explanation. I will assume you already have your API roles set up,
and you can invoke Magento API and get responses.

We will test this functionality for the order info API call. Let's start
with the API v1. In order to connect and get the response for the individual order, you need to have code like this one:
```php
<?php

    include 'app/Mage.php';

    try {
        $client = new SoapClient(Mage::getBaseUrl() . 'index.php/api/soap?wsdl=1', array('exceptions' => true, 'trace' => true));
    } catch (Exception $e) {
        header('HTTP/1.1 500');
        echo 'API unavailable.';
        exit;
    }

    $session = $client->login('ApiTest', 'test_key');
    $result = $client->call($session, 'sales_order.info', '100015226');

    echo "<pre>";
    var_dump($result);
    echo "</pre>";

    exit;
```

Save this in your root directory, and you can access it from browser.
This will send a request to the Magento asking for the information about this specific order (ID: 100015226). Where does this request go? The answer is Mage_Sales_Model_Order_Api class, and its info method. We want a custom attribute. Lets add it to the very end of this method, but not in the core. Create a module, or use an existing one and override this class and method!
```php
    public function info($orderIncrementId)
    {
        $order = $this->_initOrder($orderIncrementId);

        if ($order->getGiftMessageId() > 0) {
            $order->setGiftMessage(
                Mage::getSingleton('giftmessage/message')->load($order->getGiftMessageId())->getMessage()
            );
        }

        $result = $this->_getAttributes($order, 'order');

        $result['shipping_address'] = $this->_getAttributes($order->getShippingAddress(), 'order_address');
        $result['billing_address'] = $this->_getAttributes($order->getBillingAddress(), 'order_address');
        $result['items'] = array();

        foreach ($order->getAllItems() as $item) {
            if ($item->getGiftMessageId() > 0) {
                $item->setGiftMessage(
                    Mage::getSingleton('giftmessage/message')->load($item->getGiftMessageId())->getMessage()
                );
            }
            $result['items'][] = $this->_getAttributes($item, 'order_item');
        }

        $result['payment'] = $this->_getAttributes($order->getPayment(), 'order_payment');

        $result['status_history'] = array();

        foreach ($order->getAllStatusHistory() as $history) {
            $result['status_history'][] = $this->_getAttributes($history, 'order_status_history');
        }

        $result['custom_attribute'] = 'CUSTOM VALUE';

        return $result;
    }
```

The second-to-last line is the modification. We've added a 'custom_attribute' key with the value of 'CUSTOM VALUE'. Refresh the API script and you should see the value returned along with the other information from the order.

```php
    ["custom_attribute"]=> string(12) "CUSTOM VALUE"
```

Excellent. We have modified the Magento api method and added our own attribute. Now off to the API v2.

API v2 has some changes in the way the methods are invoked. The script that connects and sends the request now looks like this:
```php
<?php

    include 'app/Mage.php';

    $proxy = new SoapClient(Mage::getBaseUrl() . 'index.php/api/v2_soap/?wsdl');
    $sessionId = $proxy->login((object)array('username' => 'ApiTest', 'apiKey' => 'test_key'));
    $result = $proxy->salesOrderInfo((object)array('sessionId' => $sessionId->result, 'orderIncrementId' => '100015226'));

    echo "<pre>";
    var_dump($result);
    echo "</pre>";

    exit;
```

You can see that the order ID is the same, only the way the request is being sent is different. You should also go into the Magento admin `System > Configuration > Magento Core Api` and turn on the **WS-I Compliance** in order to work with v2 API.
So after some configuration, refresh the page and see what happens. We have lost our `custom_attribute`.

What happened? Does Magento go to some other method in v2 of its API? No, it goes to the same one but it has another entry point: `Mage_Sales_Model_Order_Api_V2`. You should go and override this class and let it extend from your new `Mage_Sales_Model_Order_Api` one (ie. the one we created above). Refresh the call again. What? Nothing again? Come on...

Relax, we have one more thing to do. In order to make this work you should create additional files. Let's take a look into `Mage/Sales/etc/wsdl.xml` and `Mage/Sales/etc/wsi.xml`. Search for the `salesOrderInfo` in the **wsdl.xml** and you will find this:
```xml
<message name="salesOrderInfoRequest">
    <part name="sessionId" type="xsd:string" />
    <part name="orderIncrementId" type="xsd:string" />
</message>
<message name="salesOrderInfoResponse">
    <part name="result" type="typens:salesOrderEntity" />
</message>
```

The meaning is the following: our `salesOrderInfo` request message is expected to send **sessionId** and **orderIncrementId**, both of type string to Magento. If we look into our Api script above we notice that we have done that already:
```php
    $result = $proxy->salesOrderInfo((object)array('sessionId' => $sessionId->result, 'orderIncrementId' => '100015226'));
```

As for the response, as you can see in the xml above, Magento will return the **salesOrderEntity** type back. Let's look for the **salesOrderEntity**.
```xml
<complexType name="salesOrderEntity">
    <all>
        <element name="increment_id" type="xsd:string" minOccurs="0" />
        <element name="parent_id" type="xsd:string" minOccurs="0" />
        <element name="store_id" type="xsd:string" minOccurs="0" />
        <element name="created_at" type="xsd:string" minOccurs="0" />
        <element name="updated_at" type="xsd:string" minOccurs="0" />
        <element name="is_active" type="xsd:string" minOccurs="0" />
        <element name="customer_id" type="xsd:string" minOccurs="0" />
        <element name="tax_amount" type="xsd:string" minOccurs="0" />
        <element name="shipping_amount" type="xsd:string" minOccurs="0" />
        <element name="discount_amount" type="xsd:string" minOccurs="0" />
        <element name="subtotal" type="xsd:string" minOccurs="0" />
        <element name="grand_total" type="xsd:string" minOccurs="0" />
        <element name="total_paid" type="xsd:string" minOccurs="0" />
        <element name="total_refunded" type="xsd:string" minOccurs="0" />
        <element name="total_qty_ordered" type="xsd:string" minOccurs="0" />
        <element name="total_canceled" type="xsd:string" minOccurs="0" />
        <element name="total_invoiced" type="xsd:string" minOccurs="0" />
        <element name="total_online_refunded" type="xsd:string" minOccurs="0" />
        <element name="total_offline_refunded" type="xsd:string" minOccurs="0" />
        <element name="base_tax_amount" type="xsd:string" minOccurs="0" />
        <element name="base_shipping_amount" type="xsd:string" minOccurs="0" />
        <element name="base_discount_amount" type="xsd:string" minOccurs="0" />
        <element name="base_subtotal" type="xsd:string" minOccurs="0" />
        <element name="base_grand_total" type="xsd:string" minOccurs="0" />
        <element name="base_total_paid" type="xsd:string" minOccurs="0" />
        <element name="base_total_refunded" type="xsd:string" minOccurs="0" />
        <element name="base_total_qty_ordered" type="xsd:string" minOccurs="0" />
        <element name="base_total_canceled" type="xsd:string" minOccurs="0" />
        <element name="base_total_invoiced" type="xsd:string" minOccurs="0" />
        <element name="base_total_online_refunded" type="xsd:string" minOccurs="0" />
        <element name="base_total_offline_refunded" type="xsd:string" minOccurs="0" />
        <element name="billing_address_id" type="xsd:string" minOccurs="0" />
        <element name="billing_firstname" type="xsd:string" minOccurs="0" />
        <element name="billing_lastname" type="xsd:string" minOccurs="0" />
        <element name="shipping_address_id" type="xsd:string" minOccurs="0" />
        <element name="shipping_firstname" type="xsd:string" minOccurs="0" />
        <element name="shipping_lastname" type="xsd:string" minOccurs="0" />
        <element name="billing_name" type="xsd:string" minOccurs="0" />
        <element name="shipping_name" type="xsd:string" minOccurs="0" />
        <element name="store_to_base_rate" type="xsd:string" minOccurs="0" />
        <element name="store_to_order_rate" type="xsd:string" minOccurs="0" />
        <element name="base_to_global_rate" type="xsd:string" minOccurs="0" />
        <element name="base_to_order_rate" type="xsd:string" minOccurs="0" />
        <element name="weight" type="xsd:string" minOccurs="0" />
        <element name="store_name" type="xsd:string" minOccurs="0" />
        <element name="remote_ip" type="xsd:string" minOccurs="0" />
        <element name="status" type="xsd:string" minOccurs="0" />
        <element name="state" type="xsd:string" minOccurs="0" />
        <element name="applied_rule_ids" type="xsd:string" minOccurs="0" />
        <element name="global_currency_code" type="xsd:string" minOccurs="0" />
        <element name="base_currency_code" type="xsd:string" minOccurs="0" />
        <element name="store_currency_code" type="xsd:string" minOccurs="0" />
        <element name="order_currency_code" type="xsd:string" minOccurs="0" />
        <element name="shipping_method" type="xsd:string" minOccurs="0" />
        <element name="shipping_description" type="xsd:string" minOccurs="0" />
        <element name="customer_email" type="xsd:string" minOccurs="0" />
        <element name="customer_firstname" type="xsd:string" minOccurs="0" />
        <element name="customer_lastname" type="xsd:string" minOccurs="0" />
        <element name="quote_id" type="xsd:string" minOccurs="0" />
        <element name="is_virtual" type="xsd:string" minOccurs="0" />
        <element name="customer_group_id" type="xsd:string" minOccurs="0" />
        <element name="customer_note_notify" type="xsd:string" minOccurs="0" />
        <element name="customer_is_guest" type="xsd:string" minOccurs="0" />
        <element name="email_sent" type="xsd:string" minOccurs="0" />
        <element name="order_id" type="xsd:string" minOccurs="0" />
        <element name="gift_message_id" type="xsd:string" minOccurs="0" />
        <element name="gift_message" type="xsd:string" minOccurs="0" />
        <element name="shipping_address" type="typens:salesOrderAddressEntity" minOccurs="0" />
        <element name="billing_address" type="typens:salesOrderAddressEntity" minOccurs="0" />
        <element name="items" type="typens:salesOrderItemEntityArray" minOccurs="0" />
        <element name="payment" type="typens:salesOrderPaymentEntity" minOccurs="0" />
        <element name="status_history" type="typens:salesOrderStatusHistoryEntityArray" minOccurs="0" />
    </all>
</complexType>
```

Wow, that's a big list. But we can easily see that it's elements match the response we got from the call. What Magento does is it simply looks into this file, and returns only the attributes listed here. A simple fix would be to add our own attribute into this list, and everything should be fine. But we should not do this in the core file, instead, we should create our own wsdl.xml file, because Magento merges all of his files into one big file. This way the core is not tampered and we have our functionality. So create a wsdl.xml in your module and in it put code like this one:
```xml
<definitions xmlns:typens="urn:{%raw%}{{var wsdl.name}}{%endraw%}" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
    xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/" xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/" xmlns="http://schemas.xmlsoap.org/wsdl/"
    name="{%raw%}{{var wsdl.name}}{%endraw%}" targetNamespace="urn:{%raw%}{{var wsdl.name}}{%endraw%}">
    <types>
        <schema xmlns="http://www.w3.org/2001/XMLSchema" targetNamespace="urn:Magento">
            <import namespace="http://schemas.xmlsoap.org/soap/encoding/" schemaLocation="http://schemas.xmlsoap.org/soap/encoding/"
            />
            <complexType name="salesOrderEntity">
                <all>
                    <element name="custom_attribute" type="xsd:string" minOccurs="0" />
                </all>
            </complexType>
        </schema>
    </types>
</definitions>
```
We have also mentioned wsi.xml file. This file also has a salesOrderEntity that we need to change. But
also avoid the core changes, and create the file in your own module, and paste in it something like this:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<wsdl:definitions xmlns:typens="urn:{%raw%}{{var wsdl.name}}{%endraw%}" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:soap="http://schemas.xmlsoap.org/wsdl/soap/"
    xmlns:soapenc="http://schemas.xmlsoap.org/soap/encoding/" xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/" name="{%raw%}{{var wsdl.name}}{%endraw%}"
    targetNamespace="urn:{%raw%}{{var wsdl.name}}{%endraw%}">
    <wsdl:types>
        <xsd:schema xmlns:xsd="http://www.w3.org/2001/XMLSchema" targetNamespace="urn:{%raw%}{{var wsdl.name}}{%endraw%}">
            <xsd:complexType name="salesOrderEntity">
                <xsd:sequence>
                    <xsd:element name="custom_attribute" type="xsd:string" minOccurs="0" />
                </xsd:sequence>
            </xsd:complexType>
        </xsd:schema>
    </wsdl:types>
</wsdl:definitions>
```

### Clear the (proper) cache

Ok, if you refresh the API call once again, nothing happens. We have the same response. There is just one small thing
we need to do (the thing that cost me a few hours at least). PHP is caching wsdl in a separate place (not Magento cache).
On Linux operating system it is in /tmp folder. **The files are the ones starting with the wsdl-**. Delete all of them and refresh
the script. Finally, we have our `custom_attribute` in the response array. If you don't, try clearing your Magento cache,
it helps sometimes ^^.