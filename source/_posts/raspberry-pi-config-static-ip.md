---
title: 为你的树莓派设置静态ip
tags:
  - null
  - null
categories:
  - null
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2014-4-10 17:39:33
desc:
keywords: Raspberry Pi bind mac address,树莓派绑mac
---

所以说，不管做什么都不要粗心大意，那些误删文件找不回来挖个洞躲着哭的，那些设置树莓派静态ip漏删了一句话导致pi吃了半个月灰的（冏）。。。

<!--more-->

自从上次设置树莓派静态ip漏删了一个地方，不能连接树莓派，吃灰了接近两个周，手头又没有显示器，usb转com口折腾来折腾去都是unknow device以来（这句话好长啊），今天上班时发现pi又连不上了，难道是昨天电信给我发了条短信说要维护升级随机重置wlan密码？不对，回来之后发现各种网站还是能上的（除了GF干掉的那些，例如：http://www.google.com/analytics），这是怎么一个回事。

### 情况是这样的

电信疯了我的80端口（草泥煤），遂在路由上做了8000端口转发到192.168.1.104的8000端口（树莓派的ip地址，dhcp的）。

其他端口例如putty的， vnc的也相应都做了转发。

### 但是我发现情况是这样的

光纤猫，路由都正常，唯独树莓派的ip从192.168..1.104变成了192.168.1.100，为什么？不解，难道是路由太寂寞自己重启了导致树莓派dhcp到了新地址？随便了，要不是这个事儿，我还是不会去问度娘：“树莓派静态ip”。

### 正文終于來了（轉載内容，但根據自身情況修改了一些）

**1. #vim打開網絡配置的文件**
 **sudo vim /etc/network/interfaces**

**2. #將其中的文件修改爲
****auto lo**

**iface lo inet loopback**
**iface eth0 inet static
\#iface eth0 inet dhcp**

**address 192.168.1.104**
**netmask 255.255.255.0**
**gateway 192.168.1.1**

**allow-hotplug wlan0**
**iface wlan0 inet manual**
**wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf**

**3. #重啓服務**
**sudo service networking restart**

 

到這裡就搞定，若用putty連接的話，過一會兒會提示連接不上，有可能還要重啓一下pi。

原文地址：http://blog.csdn.net/cxhome213/article/details/8983247