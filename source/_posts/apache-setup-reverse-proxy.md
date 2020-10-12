---
title: Apache反向代理Discuz地址有问题的解决方法
tags:
  - apache
categories:
  - nginx
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2014-11-6 17:14:04
desc:
keywords: Apache反向代理,Apache Discuz反向代理,您的请求来路不正确解决办法
---

机器A拥有外网ip，机器B处于内网。

机器A运行apache，本地程序，开放80端口，并且作为代理服务器处理外部泛解析域名，例如bbs.lison.com。并且解析到机器B的端口8090。

机器B运行nginx，处理8090端口的web服务。
<!--more-->

部署成功后，出现的问题是：访问bbs.lison.com之后，页面能打开，但是样式无法加载，查看源代码发现<base href=”http://机器B:8090″ />，依然是机器B的内网地址端口号。

这个问题困扰了我好几天，直到今天看到大神的文章：

Apache反向代理Discuz出现“您的请求来路不正确”解决办法

http://rayyn.net/2010/06/37.html