---
layout: post
title: Enable MySQL query logging in Magento 2
date: 2018-06-05 21:45:09.000000000 +02:00
author: luka
categories:
- magento
- mysql
---

You're probably aware of the fact that Magento is a very heavy application. It is especially hard on database, and performs a lot of queries in order to function properly (which it does not a lot of times, unfortunately).
Sooner or later, a need will arise for you to check what queries are being performed on the page. Either you're hunting for the slow ones, or are trying to optimize their number. There is a simple way to have this working.

Magento 2 comes with a cool console suite of commands than can be invoked by running `php bin/magento` in the root of your application. One of the commands listed is `dev:query-log:enable`. Let's see what it can do.

```bash
> bin/magento dev:query-log:enable --help

Usage:
  dev:query-log:enable [options]

Options:
      --include-all-queries[=INCLUDE-ALL-QUERIES]    Log all queries. [true|false] [default: "true"]
      --query-time-threshold[=QUERY-TIME-THRESHOLD]  Query time thresholds. [default: "0.001"]
      --include-call-stack[=INCLUDE-CALL-STACK]      Include call stack. [true|false] [default: "true"]
  -h, --help                                         Display this help message
  -q, --quiet                                        Do not output any message
  -V, --version                                      Display this application version
      --ansi                                         Force ANSI output
      --no-ansi                                      Disable ANSI output
  -n, --no-interaction                               Do not ask any interactive question
  -v|vv|vvv, --verbose                               Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Help:
  Enable DB query logging
```

Options are more or less self-explanatory. One of the better ones is `--include-call-stack=false` which will remove call stack for each query logged, and leave you with a simple list of SQL queries.

It's also useful for logging of slower queries (without using MySQL's slow query logging) with `--query-time-threshold` and `--include-all-queries` options.

To conclude, by running

```bash
> bin/magento dev:query-log:enable
```

Magento will start writing all the queries to **var/debug/db.log** file (Magento `var` folder, not linux).

Don't foget to stop query logging by running `dev:query-log:disable` when you don't need it anymore.

Happy debugging.

Cheers.