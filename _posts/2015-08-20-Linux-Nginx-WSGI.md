---
title: Linux配置Nginx + WSGI
time: 2015.08.20
layout: post
tags:
- 运维配置
excerpt: 最近做了一个Django的小项目, 然后又购买了一个vps, 所以要在ubuntu下将这个小项目ssh到vps上.在vps上已经装好了apache来跑一个php的项目.
---


最近在配置web服务器, 将django发布到VPS上. 中间遇到了不少问题, 也得到了一些帮助. 深感对计算机软件还有很多的不了解, 对计算机网络
linux操作系统也有很多不熟悉的地方. 

背景: 最近做了一个Django的小项目DMS, 然后又购买了一个vps, 所以要在ubuntu下将这个小项目ssh到vps上.
在vps上已经装好了apache来跑一个php的项目. 

------

过程: 
刚开始我是考虑直接用apache2来做web-server, 因为vps已经有apache的部署. 但是考虑到apache2 + mod_python部署可能会有很多问题,
而且apache2的版本有些不稳定, 所以后来开始尝试使用nginx.  在配置nginx的过程中, 我在/etc/nginx/sites-enable/目录下新建了一个dms.conf文件,
因为server_name写错, 导致nginx启动失败(开始并不知道), 后来就又开始去弄apache2 + wsgi.
参考了[apache2 + wsgi配置](http://blog.163.com/soyking@126/blog/static/162125251201392311561784/), apache2在本地已经测试OK了,
但是当scp到vps上的时候, 始终无法正确访问8088这个端口, 最后还是决定用nginx + wsgi来部署(也考虑到后续的问题).

------
下面就是nginx + wsgi的部署了.
首先先了解整个web请求的流程, nginx主进程lanuch了多个虚拟主机, 监听多个端口, 然后在VirtualHost的配置文件中, 要经过wsgi这一层,
然后由他交给django进行处理, 所以wsgi是连接nginx跟django的桥梁.

(1)当然是先要安装nginx跟wsgi,

```
apt-get install python-dev
pip install uwsgi
yum/apt install nginx
```

(2) 在DMS这个project的主目录下建立一个wsgi.py文件, 将设DMS的路径为: /home/tcoops/DMS

```
#!/usr/bin/env python
# coding: utf-8

import os
import sys
reload(sys)
sys.setdefaultencoding('utf8')

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "DMS.settings")

from django.core.handlers.wsgi import WSGIHandler
application = WSGIHandler()
```

(3) 然后在同一个目录下新建dms_uwsgi.ini文件

```
# mysite_uwsgi.ini file
[uwsgi]

# Django-related settings
# the base directory (full path)
chdir           = /home/tcoops/DMS
# Django's wsgi file
module          = wsgi                

# the virtualenv (full path)
#home            = /home/tcoops/DMS/virtualenv

# process-related settings
# master
master          = true
# maximum number of worker processes
processes       = 10
# the socket (use the full path to be safe
socket          = /home/tcoops/DMS/mysite.sock  
# ... with appropriate permissions - may be needed
chmod-socket    = 777
# clear environment on exit
pidfile = /home/tcoops/DMS/uwsgi.pid
vacuum          = true
#daemonize = /home/tcoops/DMS/DMS/log.log
```


可以用

```
uwsgi --http :8080 --ini dms_uwsgi.ini
```
测试上面的流程有没有问题


(4) 下面就是要在nginx的配置文件中指明访问的端口, django项目的目录, 以及wsgi的文件位置等信息

```
upstream django {
     server unix:///home/tcoops/DMS/mysite.sock;
    #server 127.0.0.1:8001; # for a web port socket (we'll use this first)
}

# configuration of the server
server {
    # the port your site will be served on
    listen      8088;  

    # the domain name it will serve for
    # server_name .example.com; # substitute your machine's IP address or FQDN
rset     utf-8;

    cha
    # max upload size
    client_max_body_size 75M;   # adjust to taste


    # Django media
    location /media  {
        alias /home/tcoops/DMS/DMS/media;  # your Django project's media files - amend as required 
    }


    location /static {
        alias /home/tcoops/DMS/DMS/mystatic; # your Django project's static files - amend as required
    }


    # Finally, send all non-media requests to the Django server.

    location / {
        uwsgi_pass  django; 
        include     /etc/nginx/uwsgi_params; # the uwsgi_params file you installed   
    }
}
```

最后

```
uwsgi --ini dms_uwsgi.ini
```
来启动dms_uwsgi.ini这个文件, 同时/etc/init.d/nginx restart 来重启nginx服务.

------
中间遇到了很多问题

* 配置文件中存在中文编码, 导致错误

* dms_uwsgi.ini文件里面设定的sock文件的访问权限没有chmod为777, 导致request denied.

* 对于django project的文件相对位置搞错, 将wsgi文件的位置搞错,  导致无法search到DMS.setting这个文件, 说到底,
还是对django系统存在认识误区

* 配置端口的问题, 不知道具体配置哪个文件. 比如, nginx的默认端口号为80, 那么当系统中存在一个已经占用了80端口的apache之后, 会报错, 那么需要修改的是site_enabled这个里面的default文件, 设置一下默认端口号
 
