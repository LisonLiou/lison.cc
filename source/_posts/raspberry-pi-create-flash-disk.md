---
title: 为树莓派创建内存盘，减少对SD卡寿命的消耗
tags:
  - 折腾
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
date: 2014-3-10 17:35:25
desc:
keywords: 树莓派创建内存盘
---

看到标题的时候我以为要加个硬件？长的跟小霸王游戏卡似的那种？或者跟windows似的加个U盘、硬盘转换为内存扩展盘？Oh no，我想多了。原来只有一块光棍板的我也可以为SD卡多考虑一下人生后面的事。

<!--more-->

**<font color=green>mkdir /ram</font>**
**<font color=green>mount -t tmpfs -o size=10m,mode=0777 tmpfs /ram</font>**

1. 根目录下创建ram目录

2. 挂载ram目录。mount没有用过，只知道这是个挂载命令，挂光驱、挂U盘、挂移动硬盘，有时间再戳它。

3. 以上命令是创建10兆内存盘。根据自己的需求进行调整。

 

ok, 这篇文章是抄的，原文链接：http://shumeipai.nxez.com/2013/10/04/raspberry-pi-come-in-to-create-a-memory-disk.html

极客范也有：

http://www.geekfan.net/6283/