---
layout: post
title: Change reward message in magento
date: 2014-07-08 12:59:32.000000000 +00:00
categories:
- Magento
author: luka
---
Reward messages in magento are not coming from database. They are not coming from configuration in admin. They are not set
in blocks nor templates. So where are they then?

If you want to change the reward message open up the `reward.xml` file in
your theme and look for lines like this one:
```xml
<action method="setRewardMessage" translate="message" module="enterprise_reward">
  <message>Submit a new tag now and earn %s once the tag is approved.</message>
</action>
```

Cheers