---
title: QT解决疑难杂症之打包项目运行出现"QMYSQL driver not loaded"错误
tags:
  - null
categories:
  - QT
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2022-11-16 19:13:04
desc:
keywords: Qt fix deploy QMYSQL driver not loaded
---

### 出现的问题

> 
> 本文只讨论打包之后的项目运行时遇到的找不到驱动情况，也就是在客户机上没有Qt开发环境的机器。
> 
> 即：QSqlDatabase: QMYSQL driver not loaded
> 
> 但：QSqlDatabase: available drivers: QSQLITE QMARIADB QMYSQL QMYSQL3 QODBC QODBC3 QPSQL QPSQL7
> 你说气人不？
> 
>
> 在开发机上遇到的上述错误解决之后，使用windeployqt打包之后放到没有Qt环境的客户机出现又上述错误，明明数据库也安装成功了。

### 编译环境
	1. msvc2019(32bit)
	2. MySQL Server 8.0 (C:\Program Files\MySQL\MySQL Server 8.0)
	3. MySQL Connector C 6.1 (注意这是32位的驱动，如果安装MySQL Server之后没有这个驱动，需要自行去官网下载并安装 C:\Program Files (x86)\MySQL\MySQL Connector C 6.1)

解决方法：

1. 确定使用的libmysql.dll与libmysql.lib是来自驱动文件夹。
	确保将驱动路径(C:\Program Files (x86)\MySQL\MySQL Connector C 6.1\lib)下的libmysql.dll与libmysql.lib拷贝至exe同级路径。

2. 编译qsqlmysql.dll与qsqlmysql.lib确保引入的内容来自驱动路径
	什么是驱动路径：假如安装的是MySql Server 8.0，那么在编译qsqlmysql.dll与qsqlmysql.lib时，项目的pro文件中确保引用的是驱动路径下的include与lib路径：
	
  ```
  INCLUDEPATH += "C:\Program Files (x86)\MySQL\MySQL Connector C 6.1\include"
  LIBS += "C:\Program Files (x86)\MySQL\MySQL Connector C 6.1\lib\libmysql.lib"
  ```

3. 解决SSL​“SSL connection error: unknown error number
	上述问题都解决之后，不再出现driver not loaded错误，而出现了ssl connection error，打开msql敲入
  ```
  show variables like'%ssl%'
```

  看到have_openssl和have_ssl都是YES。
	找到my.ini，在[mysqld]下加入 skip_ssl。
	如果在MySQL Server路径下找不到my.ini，可以尝试去ProgramData\MySQL路径寻找，或者直接查看MySQL Client cmd的属性也会找到对应的启动参数。


<!--more-->