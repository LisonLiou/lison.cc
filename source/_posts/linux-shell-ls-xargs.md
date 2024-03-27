---
title: Linux Shell 使用一条命令批量重命名文件
tags:
  - bash
categories:
  - Linux Shell
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2024-03-27 17:45:07
desc:
keywords: Linux ls管道xargs批量重命名
---


<!--more-->

> 假如一个文件夹中有这样的文件
```
1.patch
2.patch
3.patch
...
n.patch
```
> 需要将他们命名为
```
1.patch.bak
2.patch.bak
3.patch.bak
...
n.patch.bak
```

> 你不会想写个for循环什么的吧。。。用下面这条xargs组合靓命令即可解决

```
  ls *.patch | xargs -I {} mv {} {}.bak
```

> xargs会取ls的每一个输出，并将其替换为{}，然后执行后面的命令。这样，每个文件都会被单独处理。

> 如果你想批量为他们修改权限，则可以这样写(只是举个栗子)
```
  ls *.patch | xargs -I {} chmod 644 {}
```
