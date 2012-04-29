---
layout: post
title: "open_basedir restriction in effect: eAccelerator and open_basedir"
author: Infong
category: spl
tags: [apache, nginx, eAccelerator, open_basedir, php]
---

When I was checking my server's apache error log, I found many warning about 'open\_basedir' like:

    2011/09/25 21:27:54 [error] 3927#0: *146 FastCGI sent in stderr: "PHP Warning: require(): open_basedir restriction in effect. File() is not within the allowed path(s): (/srv/http/:/home/:/tmp/:/usr/share/pear/) in /srv/http/xxx.php on line 913″ while reading upstream, client: x.x.x.x, server: xxxxx.com, request: "GET / HTTP/1.1", upstream: "fastcgi://unix:/var/run/php-fpm/php-fpm.sock:", host: "xxxxx.com", referrer: "http://xxxxx.com/"

I tried everything including setting setting open\_basedir. It stopped working if I activated the open\_basedir protection and started working if I removed it. After doing some research, I looked into the wrong direction – the problem was not php but eAccelerator. After I found that out it was easy to get to the solution, you need to compile it with the option --without-eaccelerator-use-inode, like this:

{% highlight bash %}
$ cd /PATH/TO/eaccelerator-$VERSION
$ make clean
$ ./configure --enable-eaccelerator=shared --without-eaccelerator-use-inode
$ make
$ sudo make install
{% endhighlight %}

After re-compiling and installing, make sure you clear out the existing eAccelerator files before restarting webserver:

{% highlight bash %}
$ sudo rm -rf /var/cache/eaccelerator/*
$ sudo /etc/rc.d/httpd restart
{% endhighlight %}

OK, everything became quiet again.

Reference:

1.  http://eaccelerator.net/ticket/414
2.  http://eaccelerator.net/ticket/104
