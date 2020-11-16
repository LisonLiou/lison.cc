---
title: Python 文件内容读取示例 Python open打开操作文件
tags:
categories:
  - python
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2017-4-10 11:09:52
desc: python内置方法操作文件示例，python open方法简单使用示例，python处理文件内容示例，python字符串分割函数split使用示例
keywords: Python 文件内容读取示例 Python open打开操作文件 python操作文件,python open使用,python处理文件内容
---

整理了一些数据需要批量更新到数据库，内容如：

001    009
002   008
003   007

要生成的语句：update table_name set column=009 where id=001

使用python一共三行语句：

<!--more-->

```
for line in open("data", 'r'):
    arr = str(line).split()
    print('update xx set region=\'' + arr[1] + '\' where no=\'' + arr[0] + '\'')
```

1.  open方法是内置方法（Build-in function），文档解释为：Open file and return a corresponding file object，打开文件并且返回该文件对象。

2. 将line转换为string并分割成数组，split方法若不加任何参数则默认使用任何空白字符（空格，制表符等）进行分割，并且自动去处数组内的空白元素，太方便了，split官方说明：

   S.split(sep=None, maxsplit=-1) -> list of strings

   *Return a list of the words in S, using sep as the*
   *delimiter string. If maxsplit is given, at most maxsplit*
   *splits are done. If sep is not specified or is None, any*
   *whitespace string is a separator and empty strings are*
   *removed from the result.*

3. 这行就是组织sql语句了

人生苦短，我用python