---
layout: post
title: 'Negotiation: discovered file(s) matching request: <> (None could be negotiated).'
date: 2014-08-17 21:19:24.000000000 +00:00
categories:
- Apache
- Magento
- PHP
- linux
tags:
- Apache
author: luka
---
I had this error on my local apache server. While trying to invoke some Magento API,apache log showed this error. To get
rif of it, simply remove the **MultiViews** part in you apache virtualhost file. For me it was in

    /etc/apache2/sites-enabled/<your_vhost.conf>
    
and that was it.