---
title: 树莓派（Raspberry Pi）托管wordpress – php5+mysql5+nginx
date: 2014-2-27 15:26:15
tags: 
	- 折腾
	- 树莓派
categories: 
	- 树莓派
desc: 使用树莓派托管wordpress过程记录，环境搭建过坑。
keywords: 树莓派托管wordpress、树莓派php环境搭建、树莓派运行nginx、树莓派mysql部署

---

这是一篇转载文，记录了我的pi正在运行的wordpress，转载的应该是一篇翻译的文章，照着它，实现了linux下wordpress站点的部署，即php运行环境的搭建。

<!--more-->

这是一篇转载文，记录了我的pi正在运行的wordpress，转载的应该是一篇翻译的文章，照着它，实现了linux下wordpress站点的部署，即php运行环境的搭建。以下为原文内容：部分修改有备注。

Raspberry Pi到手一周了，搭了个服务器，因为Raspberry Pi硬件配置不高，所有选择的是nginx+mysql+php，基本是按照如下参考网站弄的，但网站上的步骤有点问题，做了一些修改。如果你觉得自己搭服务器麻烦，也可以下载如下网站已经做好的固件，刷如sd卡，开机启动后找到树莓派的ip就可以了。

参考网站：http://www.cnx-software.com/2012/08/03/wordpress-for-raspberry-pi-using-nginx-and-mysql/

Raspberry Pi的固件有很多，我安装的是官方的Raspbian，具体安装设置方法请参考

[树莓派RASPBERRY PI上手报告| 雷锋网](http://www.leiphone.com/raspberry-pi-hands-on.html)

一切准备就绪后就可以开机了，开机后启动终端，输入如下代码，建议使用root权限

**sudo apt-get update
sudo apt-get install nginx php5-fpm php5-cli php5-curl php5-gd php5-mcrypt php5-mysql php5-cgi mysql-server **


期间会提示设置mysql密码，下载安装好nginx和mysql后在/etc/nginx/sites-available/目录下创建文件wordpress写入如下代码并保存。

``` # Upstream to abstract backend connection(s) for php
upstream php {
	server unix:/var/run/php5-fpm.sock;
}

server {
	## Your only path reference.
	root /srv/www/wordpress/public_html;
	listen 80;
	## Your website name goes here. Change to domain.ltd in VPS
	server_name _;

	access_log /srv/www/wordpress/logs/access.log;
	error_log /srv/www/wordpress/logs/error.log;
	
	## This should be in your http block and if it is, it’s not needed 	here.
	index index.php;
	
	location = /favicon.ico {
	log_not_found off;
	access_log off;
	}
	
	location = /robots.txt {
	allow all;
	log_not_found off;
	access_log off;
	}
	
	location / {
	# This is cool because no php is touched for static content
try_files $uri $uri/ /index.php;
	}
	location ~ \.php$ {
		#NOTE: You should have “cgi.fix_pathinfo = 0;” in php.ini
		include fastcgi_params;
		fastcgi_intercept_errors on;
		fastcgi_pass php;
	}

	location ~* \.(js|css|png|jpg|jpeg|gif|ico)$ {
		expires max;
		log_not_found off;
	}
}
```

然后将此文件复制到**/etc/nginx/sites-sites-enabled/**目录下，分别删除两个文件夹中的default文件。

**上述粗体路径应该是：<font color=red>/etc/nginx/sites-enabled/</font>**
然后是下载和解压wordpress（代码已修改为下载最新中文版wordpress）

**sudo mkdir -p /srv/www/wordpress/logs/
sudo mkdir -p /srv/www/wordpress/public_html
cd /srv/www/wordpress/public_html
sudo wget http://cn.wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo mv wordpress/\* .**

上述红字部分应为为：**sudo wget http://cn.wordpress.org/wordpress-3.8.1-zh_CN.tar.gz**

**设置mysql数据库（其中的raspi为wordpress数据库的密码）**

```
$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor. Commands end with ; or \g.
Your MySQL connection id is 5340 to server version: 3.23.54

Type ‘help;’ or ‘\h’ for help. Type ‘\c’ to clear the buffer.

mysql> CREATE DATABASE wordpress;
Query OK, 1 row affected (0.00 sec)

mysql> GRANT ALL PRIVILEGES ON wordpress.* TO “wordpress”@”localhost”IDENTIFIED BY “raspi”;
Query OK, 0 rows affected (0.00 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)

mysql> EXIT
Bye
$
```

将/srv/www/wordpress/public_html目录下的wp-config-sample.php文件名改为wp-config.php然后打开修改其中的以下几行

define(‘DB_NAME’, ‘wordpress’);

define(‘DB_USER’, ‘wordpress’);

define(‘DB_PASSWORD’, ‘raspi’);

重启 nginx 和 php5-fpm

**sudo cp wp-config-sample.php wp-config.php
sudo edit wp-config.php**

在浏览器中输入[http://你的树莓派ip/wp-admin/install.php](http://xn--ip-0p3cm05gkyf6qpwrv/wp-admin/install.php)，就可以安装wordpress啦！

我使用的是网通的1M ADSL，动态域名解析用的是花生壳和它提供的免费二级域名

最后欢迎大家访问我托管在树莓派上的博客：xgmlab.oicp.net(速度可能会有点慢！)

注

nginx的启动与重启

sudo nginx -s stop && nginx

通过上述配置，已经成功实现了在我的pi上运行wordpress，不过速度确实比较慢，内网就已感觉出来了，不知道外网会如何。

目前就使用这个免费的三级域名吧，以后的关于pi与linux的东西会记录在这个博客上，慢慢积累点东西。