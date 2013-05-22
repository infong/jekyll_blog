---
layout: post
title: "Ubuntu postfix errot - postdrop: warning: unable to look up public/pickup: No such file or directory"
author: Infong
category: os
tags: [linux, postfix]
---


I installed postfix and got this error:

> postdrop: warning: unable to look up public/pickup: No such file or directory.

Turns out that sendmail was previously installed and that was messing things up. I had to stop sendmail and make the appropriate directory and restart postfix.

Specifically:

    mkfifo /var/spool/postfix/public/pickup
    ps aux | grep mail
    kill
    sudo /etc/init.d/postfix restart
