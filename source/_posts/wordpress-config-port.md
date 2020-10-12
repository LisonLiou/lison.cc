---
title: wordpress站点修改域名端口号（默认端口号修改）
tags:
  - 折腾
categories:
  - wordpress
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2014-3-3 16:56:21
desc: 
keywords: wordpress修改端口号,wordpress默认端口号怎么修改
---

OK，小打小闹，在Pi上部署了wordpress，成功。无奈电信疯掉了我的80端口，所以需要修改为别的端口，于是使用8000，据说8080也被疯掉了。

<!--more-->

OK，小打小闹，在Pi上部署了wordpress，成功。无奈电信疯掉了我的80端口，所以需要修改为别的端口，于是使用8000，据说8080也被疯掉了。

### 于是乎：

1. 路由上做端口映射，8000映射到我的pi

2. 修改nginx相关文件：/etc/nginx/sites-enabled/wordpress与/etc/nginx/sites-available/wordpress，将其中的默认端口号80修改为8000.

3. 重启nginx：sudo nginx -s reload，重启php5-fpm：sudo service php5-fpm reload，成功。

浏览器敲入地址：http://lison.cc。。。。不行，打不开。

### 什么原因：

1. <font  color=green>数据库连不上？root和wordpress用户使用命令方式登录 mysql -u root -p 加密码都能进入，新建test.php，输入：<?php phpinfo(); ?>，也能正常打开。新建一个连接数据库的页面，也TMD能连上。所以这个排除掉。</font>

2. <font color=green>8000端口没映射成功？外部网络输入命令：telnet lisonliou.gicp.net 8000，直接进入，说明已成功。Ctrl+C之后提示400（这是为什么，不知道）。</font>

3. 其他的想不到了。

后来各种谷歌度娘问：nginx 怎么修改端口？因为以前windows下部署ngxin可以直接修改目录下的nginx.conf的listen节点，现在nginx.conf中没有这个选项，于是，修改了上述/etc/nginx/sites-available(enabled)/wordpress这两个文件。怎么改也不行，难道是我重启nginx，php-fpm的方式不对？难道是我打开的方式不对？难道是。。。bla bla bla。

上面绿色的第一项倒腾了好久，进入了误区，又是wp-config.php开启debug，开启表修复什么的。而且始终在以域名+端口方式访问，并没有加入具体的文件名，后来加入文件名访问（http://lison.cc/wp-login.php）发现php没问题，mysql也能连上，但是css没有加载上，于是查看源代码，发现css的连接地址都没有加入端口号，ok，这下问度娘就有方向了：”wordpress修改端口”，于是，找到了下面文章的内容，以下是内容片段：

> *wordpress关于端口的不在配置文件里，而在数据表wp_options中的siteurl和home两个变量*
> *mysql> update wp_options set option_value = ‘http://sas.123.com:8001’ where option_id = 1;*
> *mysql> update wp_options set option_value = ‘http://sas.123.com:8001’ where option_id = 37;*
>
> *原有文章中如果有插图，则插图的URL地址要更新* 
> *mysql> UPDATE wp_posts SET post_content = REPLACE( post_content, “[http://sas.123.com](http://sas.123.com/)“, “[http://sas.123.com:8001](http://sas.123.com:8001/)” ) where post_date < “2011-09-15”;*

 

** <font color=red>遗留的问题：</font>**

**1. <font color=red>现在正在编辑的文章不知道能不能提交成功。</font>**

**2. <font color=red>直接敲入域名http://lison.cc还是打不开</font>**

** <font color=red>http://lison.cc/index.php也打不开。而且自动导向到了：http://lison.cc，火狐给出的提示是：服务器响应时间过长。**</font>

这是为什么，先不管了，反正端口号已经修改成功了，先Ctrl+C一下，面的提交保存不了。

解决遗留的问题：

1.看来是已经发布成功了。

2.在wordpress的wp_opitons表中还有一项需要update：

update wp_options set option_value=’http://lison.cc’ where option_id=36; 这个36是干什么的？不知道，这个wp_options表是干什么的，是wp系统配置表？不知道。