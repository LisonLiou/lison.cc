---
title: 为wordpress配置发送邮件功能
tags:
  - 折腾
categories:
  - wordpress
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2014-3-4 17:25:04
desc:
keywords: wordpress发送邮件,wordpress不能发邮件,wordpress发邮件504
---

树莓派上搞定wordpress之后，设置了两个强密码，mysql的，pi登录的（为什么是强密码？你懂的），结果登录wordpress后台的时候发现管理员密码忘了，如何办？找回密码吧，于是点击找回密码，填入邮箱之后系统提示我：

<!--more-->

> 无法发送电子邮件。
> 可能原因：您的主机禁用了mail()函数。

我次奥，这可如何是好，刚装完而已，当然什么都没配置，什么都没有了，完全一光腚。于是问完度娘问谷歌，做法是直接update数据库表，因为密码是普通的md5加密。。。可我觉得这个方法不大好，于是我想了想，又想起来了。。。

于是我的需求来了：邮件发送功能。

1. 在我忘记密码的时候它，可以发送重置密码邮件到我的秘密邮箱。

2. 在我回复用户的评论的时候用户可以从它的秘密邮箱收到，并且有链接，他可以点击回到网站，增加用户粘性，SEO bla bla bla。。。

需求实现：

度娘跟谷歌很多帖子都说使用插件来搞定：

- wp-mail-smtp.zip，发邮件的
- comment reply notification.zip，评论回复通知的

但这不是我想要的，因为我不是租的空间，我tm有自己的服务器（虽然是小pi），还是电信（你妈逼）专线接入入入入入，我想要的是启用linux（Raspbian）的mail函数数数数数数。。。（突然间爽了）

于是我看了一遍phpinfo(); 发现好像该启的都启了，但是我敲入sendmail却提示我：command not found。这个sendmail字面看来好像就是我要的东西，于是又问度娘，度娘说：看（引用地址）====>http://www.sjyhome.com/wordpress/wp-cant-email.html

原来这厮是要装的啊，原来raspbian里没预置这货啊，那个yum怎么用啊，我这不是cent os啊，debian下用什么命令啊。

ok，raspbian是debian的什么什么儿子，于是问度娘就直接说：debian sendmail（其实我是想知道debian的安装命令是什么，yum不会用啊，我到底要菜刀什么时候啊）。

于是度娘教会我：

部分内容引自：http://www.sjyhome.com/wordpress/wp-cant-email.html（WordPress不能发送邮件，折腾的心酸史）

**1. sudo apt-get install sendmail（其实我会用apt-get，不过想不起来了）**

**2. 重启php-fpm：sudo service /etc/init.d/php5-fpm restart （我的是php5-fpm，跟上面教程的不是很一样，可能版本问题）**

**3. 查看sendmail是否安装成功：/etc/init.d/sendmail status（没有加sudo），用putty运行的命令，过了很久都没有回应。我没有按Ctrl+C，而是选择了Duplicate Session，登录之后查看了一下/etc/init.d文件夹，发现sendmail安静的躺在那里，我没有作声。心里想，次奥，这到底是成没成功，于是又回到http://lison.cc/wp-login.php，点击了找回密码，又做了一遍最开始的动作，结果在我点击获取新密码按钮火狐的当前标签页转了几秒钟后告诉我：**

# 504 Gateway Time-out

------



nginx/1.2.1








**4.于是我又了想起来：wp的后台设置（撰写）邮件服务器的地方还空着，空着。。。一切仿佛又回到了最开始，那天下午。。。**



**5.后来我设置好了邮件服务器（我确定用户名密码是对的），然后又重复了刚才的找回密码动作：结果还是提示我：**

 

# 504 Gateway Time-out

 

------



nginx/1.2.1



**我的设置看起来是这样的：**



**邮件服务器：smtp.qq.com**

**端口号：110**

**用户名：lison.liou@qq.com**

**密码：\**\**\**\**\**\**\**\***

- 难道默认的110端口不行？但是我这里没法启用SSL啊（或者目前我不想启用）

**。。。。。。我又走入了误区，全因我不能全神贯注的脑，和走马观花的眼。上面那个第四条对应的功能的是：快速发布，通过邮件发布文章配置的地方，也就是说，我走错门了，不好意思打扰了。**

”那我装完了sendmail（执行sendmail status还是没有反应）要哪里做配置啊“，我是这么问度娘的（保存一下，抽根烟先）

找到教程：http://www.server110.com/sendmail/201308/385.html

原来要加入host：sudo vim /etc/hostname

使其快速生效：sudo hostname -F /etc/hostname

查看是否生效：hostname（没有生效，为什么，不管了）

后面要配置mx转向，我的域名是oray免费赠我的，于是我添加mx纪录：lisonliou.gicp.net:8000，提示我不正确的域名，我早知道免费域名不能那么多功能了，到这里算是真的卡住我了。

哎，还是老老实实的用wp插件吧，一切又回到最开始。。。

PS： 自己的三分小地上做点写点还是挺有意思的