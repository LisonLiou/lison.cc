---
title: Debian 10 设置nginx开机自启
tags:
  - nginx
  - debian
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
date: 2020-11-11 20:21:54
desc:
keywords:
---

直接编辑 ~/.bashrc，加入以下代码：

```
SERVICE=’nginx’

if pgrep $SERVICE > /dev/null
then
echo “$SERVICE service running, everything is fine”
else
echo “$SERVICE is not running, starting”
nginx
echo “$SERVICE started”
fi
```

<!--more-->