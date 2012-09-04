---
layout: post
title: "使用Unicorn作为Ruby on Rails的服务器"
author: Infong
category: spl
tags: [ruby, rails, passenger, unicorn, ruby on rails]
---

之前有用 Nginx + passenger 作为 Ruby on Rails的服务器，但因为我的 Ruby 是用 rvm 装的，所以，每次都得重新编译 Nginx，显得比较麻烦。

所以打算试试用 Unicorn 来作为 ror 的后端。

安装unicorn:

    sudo gem install unicorn

创建网站配置文件(myproject是项目名称):

    sudo mkdir /etc/unicorn
    cd /etc/unicorn/
    sudo nano /etc/unicorn/myproject.conf

在网站里再创建一个unicorn配置文件

    nano /www/myproject/config/unicorn.rb

内容如下：

    # Minimal sample configuration file for Unicorn (not Rack) when used
    # with daemonization (unicorn -D) started in your working directory.
    #
    # See http://unicorn.bogomips.org/Unicorn/Configurator.html for complete
    # documentation.
    # See also http://unicorn.bogomips.org/examples/unicorn.conf.rb for
    # a more verbose configuration using more features.

    app_path = "/www/myproject"

    listen 8080 # by default Unicorn listens on port 8080
    worker_processes 2 # this should be >= nr_cpus
    pid "#{app_path}/tmp/pids/unicorn.pid"
    stderr_path "#{app_path}/log/unicorn.log"
    stdout_path "#{app_path}/log/unicorn.log"

设置unicorn启动脚本：

可以参考[官方的示例][link1]

启动unicron`/path/to/init.sh start`

修改nginx的配置文件，加入unicorn的代理设置：

可以参考[官方的示例][link2]

[link1]: http://unicorn.bogomips.org/examples/init.sh
[link2]: http://unicorn.bogomips.org/examples/nginx.conf
