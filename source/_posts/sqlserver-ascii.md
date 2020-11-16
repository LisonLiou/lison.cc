---
title: sqlserver生成字符串获取ASCII码
tags:
categories:
  - t-sql
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2016-4-26 17:18:47
desc:
keywords: sqlserver 获取字符串ASCII码
---

给APP上加个彩蛋，又不想那么明目张胆，用char吧，得生成ASCII码。

鱼饰就有了下面的代码，循环字符串长度并且substring，有点搓，还能用吧。

<!--more-->

```
declare @msg varchar(255)
set @msg='Hey man, What is the fuck r u doing ?'

declare @i int
set @i=0

declare @result varchar(255)
while @i<=len(@msg)
begin
	print ',' + str(ASCII(substring(@msg,@i,1)))	
	set @i=@i+1
end
```