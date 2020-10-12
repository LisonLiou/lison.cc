---
title: wordpress加入百度统计代码（转载）
date: 2014-2-27 16:41:43
tags: 
	- 折腾
categories:
	- wordpress
desc: 
keywords: 
---

wordpress加入百度统计代码具体实现方法记录一下。

<!--more-->

wordpress使用经验不多，之前以为wordpress有插件之类的东西可以一键搞定，结果还是手动添加代码的方式。如下：

找到footer.php文件，在wp-content/themes/xx/目录下(xx是主题文件)，把生成的统计代码放到 </body>前面。

生成的代码：

```
<script type=”text/javascript”>var _bdhmProtocol = ((“https:” == document.location.protocol) ? ” https://” : ” http://”);document.write(unescape(“%3Cscript src='” + _bdhmProtocol + “hm.baidu.com/h.js%3F0ad8a80adfac4d244e679f2c011b45bf’ type=’text/javascript’%3E%3C/script%3E”));</script>
```

修改后更新文件，到统计后台检查代码安装情况，系统会告诉是否安装成功。

原文地址：http://www.wellbegin.com/wordpress-how-to-join-baidu-statistics/