---
title: 在ubuntu上搭建基于TrinityCore的魔兽私服的踩坑实践
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
date: 2019-1-16 17:37:49
desc:
keywords:
---

前人种树，后人乘凉。
感谢大神“David栗子”的文章分享[《在Ubuntu上搭建基于TrinityCore的魔兽私服》。](https://www.jianshu.com/p/b442bb980117)

<!--more-->

1.  一步一步照着大神的指引来搭建服务器，一切都很顺利，直到将代码clone下来make的时候，不管make多少遍最后100%的时候都会提示错误，最后在股沟的帮助下发现用的是ubuntu16，而官方支持的最低版本的ubuntu是17，ok怪我，我升级一个。
2.  我觉得没有必要抽取所有的地图文件，真心。。。（若不抽取，记得对应修改config文件）
3.  服务端准备就绪，客户端连接成功，服务器也没有显示离线状态，点击服务器之后过一会儿又返回又选择服务器，无线循环，卡在服务器选择页面。排错：修改auth数据库的realmlist表，将ip修改为你的服务器的ip（如果你挂在外网的话）。
4.  安全组要加入允许8085和3724

参考资料：
1.[在Ubuntu上搭建基于TrinityCore的魔兽私服](https://www.jianshu.com/p/b442bb980117)
2.[Debian 9下安装测试基于TrinityCore的魔兽世界服务端](https://www.jianshu.com/p/a38864530275)
3.[基于TrinityCore在linux上搭建wow私服](http://happypeng.com/blog/基于trinitycore在linux上搭建wow私服/)
4.[魔兽世界私服Trinity，从源码开始](http://log4think.com/setup_wow_private_server/)