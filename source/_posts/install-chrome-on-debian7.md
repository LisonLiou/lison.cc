---
title: debian安装google-chrome
tags:
  - 折腾
categories:
  - linux
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2014-7-25 16:36:51
desc:
keywords: debian安装google-chrome,debian安装谷歌浏览器,dpkg 基本用法
---

还是简单的记录下吧，两天前刚装的今天又忘了。
<!--more-->

1. 首先得到*.deb的压缩包，无论是从google.com还是google.com.hk。其实比较靠谱的还是：

   For 32 bit:

> wget https://dl.google.com/linux/direct/google-chrome-stable_current_i386.deb

​	For 64 bit:

> wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb

#通常，这个地址也能打开：http://www.google.cn/chrome/（手动下载地址）

2. 执行安装命令

``` sudo dpkg -i ./google-chrome-stable_current_i386.deb```

3. 如果有遇到错误提示，直接执行以下命令来安装chrome浏览器需要的类库。

``` sudo apt-get -f install```




文章出自：www.freehao123.com/linux-chrome/ 免费资源部落

