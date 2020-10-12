---
title: what happen to my hostname (raspberry pi)
tags:
  - raspberry pi
categories:
  - 树莓派
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2014-3-3 17:30:14
desc:
keywords: 树莓派入门,Raspberry Pi hostname
---

*事由：昨日，因故poweroff了pi（正常的），然后又poweron了，结果putty能连上，vncviewer打不开了。于是putty连上pi，执行vncserver，提示我：Syntax error：”(“。。。，具体的错误记不清了，有个fontpath之类的字眼。*

WHY：没有修改vnc的什么配置，就是第一次安装的时候设置了密码，后来也没动它，至于是不是开启自启的也更不用管了。后来问了度娘很多次，终于在谷歌上找到了答案（具体链接地址找不着了）：

<!--more-->

正常情况下命令提示符是这样的：pi@raspberry ~ $

而当时我的命令提示符是这样的：pi@(none) ~ $

因此，可以敲入hostname查看当前的hostname到底是什么，如果有不对，可以执行 **sudo vim /etc/hostname**，当时可能是戳nginx的时候多加入了一行：lisonliou.gicp.net:8000，不好用之后也忘了去掉，结果导致今天这么多的问题：哎，我还要菜刀什么时候~~~