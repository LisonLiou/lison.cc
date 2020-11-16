---
title: javascript使用persistjs跨页面存储读取数据
tags:
categories:
  - javascript
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2017-8-11 15:01:10
desc:
keywords: js跨页面存储数据,js跨页面共享数据,js 跨页面的全局变量
---

折腾记：

两个html文件html1和html2，要求使用js在html1中保存数据在html2中能够读取出来，不用数据库、不用session、不用xxx。

**用[PersistJs](https://github.com/jeremydurham/persist-js)这个js库。**

<!--more-->

html1文件的内容：

```
<input type="text" id="txt"/>
<button onclick="save()">STORE</button>

<script src="<?php echo BASE_JS_PATH ?>persist-min.js"/>

<script type="text/javascript">

    function save() {
        var store = new Persist.Store('store_name');
        console.log(store.set('data', document.getElementById('txt').value));
    }
   
</script>
```

html2文件内容：

```
<script src="<?php echo BASE_JS_PATH ?>persist-min.js"></script>
<script type="text/javascript">

    var store = new Persist.Store('store_name');
    console.log(store.get("data"));

</script>
```

剩下的就在控制台查看效果吧。

本文严重符合伸手COPY党口味，之前有见到大神的[PersistJs Demo文章](http://blog.csdn.net/yuanyuan_sun/article/details/38385427)，虽然就几句话，但是一直调试不通，自己折腾了十好几分钟才ok，记下。

据说这厮跨浏览器通用，而且若不支持html5，则自动使用cookie存储（我没验证），真是居家旅行、杀人放火必备之精品啊。