---
layout: post
title: Short introduction to ssh-copy-id and ssh config files
date: 2016-07-13 21:28:09.000000000 +00:00
categories:
- ssh
- server
- linux
author: luka
---
When you're working on many different servers, you might get tired of constantly looking for an IP address, or a password to log in. This makes the whole process very tiring.. Ctrl+R on linux can be helpful if you remember a part of the hostname, but remembering the password is almost impossible - of course if you use a secure one.

In order to speed this process up, we could use public key authentication. How does it work? Well, instead of server asking a password from you, he will have a list of public keys representing devices that are allowed to log in without any other authentication. And if one of the public keys is yours, you'll log in very easily.

So, how do we get our public key there? SSH software has a great tool called ssh-copy-id. Its man page `man ssh-copy-id` is very descriptive if you need more info, but basically, the tool gets your public key and copies it over to the remote servers `~/.ssh/authorized_keys` file. It will most likely prompt you for a password, but this is the only time you'll need to enter one. Your command should look like this:

    ssh-copy-id [-i /path/to/public_key] username@hostname

After you've authorized yourself, you can simply log in by a regular ssh command:

    ssh username@hostname

and this time, no password will be needed. This makes our life easier. No need to remember the password when connecting from this device. OK, this solves one problem.

Next, we do not want to remember many IP addresses, nor usernames, in order to quickly log in. Simple solution would be to add aliases in .bashrc or .bash_profiles like

    alias myserver='ssh me@myserver'

simple 'myserver' in terminal would have us SSHed there. This might be fine but SSH has another tool on its belt: config file. Located in your users .ssh folder, its name is 'config'. If it does not exist, create one. Here is one simple entry you can add in there:

    Host [alias_name] [can have many of them]
    HostName [ip_address|hostname]
    User [username]
    Port [port number]
    IdentityFile [public_key used to connect]

After you set this up, all you need to do is

    ssh [alias_name]

and you'll be logged in. Config file can have many entries, for all your servers.

Great thing about config file is it works with scp command as well. Need a file copied to one of your servers?

    scp this_file.zip myserver:/home/me/

and voila. It is there.

Cheers.