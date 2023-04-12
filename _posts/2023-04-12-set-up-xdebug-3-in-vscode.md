---
layout: post
title: Set up Xdebug 3 in vscode
date: 2023-04-12 10:00:00.000000000 +01:00
author: luka
categories:
- PHP
- linux
---

__Xdebug 3__ has some major differences in configuration, when compared to older versions. I'm showing how to set it up on your dockerized environment.

First, make sure xdebug is properly installed, by checking your phpinfo() response. If search for __"xdebug"__ yields no results, then it's not installed.

Keep in mind that xdebug via web server (nginx, or apache), and via console might use different configuration, so if xdebug is properly installed and configured for the web server, it might not be for a console environment.

To verify whether it's there for cli, do a ```php -i | grep xdebug``` in your console. If there is some mention, you're probably good to go.

Now for the configuration, my stack is split into multiple docker containers, all belonging to the same network. Where php is run via php-fpm. Configuration in xdebug ini (you can check the specific file path from phpinfo() response) looks like this:

```php
; Enable xdebug extension module
zend_extension=xdebug.so
xdebug.client_port = 9003
xdebug.mode=debug
xdebug.start_with_request=yes
xdebug.client_host=172.17.0.1
xdebug.idekey = VSCODE

xdebug.show_local_vars="on"
xdebug.var_display_max_depth=3
xdebug.max_nesting_level=250
xdebug.show_error_trace=1
xdebug.log=/var/log/xdebug.log
```

The IP address is an address of a host machine, as seen from the docker container. On linux, it's usually the same one, but you can check it by running this on your host: ```docker network inspect bridge``` and look for the __gateway__ IP address (__IPAM.Config.Gateway__).

For vscode side, use the "PHP Debug" extension: [Link](https://marketplace.visualstudio.com/items?itemName=xdebug.php-debug)

And the relevant part of the configuration is the following:

```json
...
{
    "name": "Listen for Xdebug",
    "type": "php",
    "request": "launch",
    "port": 9003,
    "runtimeExecutable": "/usr/bin/php",
    "pathMappings": {
        "/var/www/html": "${workspaceFolder}",
        "/var/www/html/public": "${workspaceFolder}/public"
    }
},
...
```

Open the configuration by clicking on the "gear" icon when on the debug tab in vscode. This would open the __launch.json__ file.

This should do it. If you have issues, check the logs when trying to connect. Logs are at ```/var/log/xdebug.log``` (as configured above).


> It's up to you to make your own decisions in life. That's what separates people and robots from animals... and animal robots.
>
> -- <cite>Phillip J. Fry</cite>