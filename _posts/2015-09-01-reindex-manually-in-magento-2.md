---
layout: post
title: Reindex manually in Magento 2
date: 2015-09-01 23:03:56.000000000 +00:00
categories:
- Magento2
- shell
tags:
- indexers
- Magento
- magento2
- PHP
author: luka
---
Maybe you've noticed that Magento 2 does not allow you to reindex data from the administration side. Additionally, there is no script called 'indexer.php' or something like that, that you would usually call. How do you reindex manually then? Use magento command line tool:

In your root magento directory type this:

    php bin/magento indexer:reindex

This will perform a full reindex. If you wish to reindex only one of the indexers, command is as follows:

    php bin/magento indexer:reindex indexer_name

where indexer_name can be found by typing:

    php bin/magento indexer:info

And that is pretty much it.

Cheers.