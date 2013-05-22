---
layout: post
title: Create new group/user on FreeBSD, and other
author: Infong
category: os
tags: [FreeBSD]
---

### Some FreeBSD command.

Add a new user: 

> adduser USERNAME

Add a new group: 

> pw groupadd GROUPNAME

Add user to group: 

> pw groupmod GROUPNAME -m USERNAME

Show group information: 

> pw groupshow GROUPNAME

Delete a user: 

> pw userdell USERNAME || pw userdell UID || rmuser username

------------------------------------------

If you want to use 'su -' to root, we should add the user to group 'wheel'.

------------------------------------------

By default, root does not allow login with SSH, if you want to allow root login, you should modify '/etc/ssh/sshd_config' as follow:

    PermitRootLogin yes
    PasswordAuthentication yes
    AllowUsers root
