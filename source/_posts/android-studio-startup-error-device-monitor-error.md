---
title: Android Studio 启动 Device Monitor报错：An error has occurred. See the log file...
tags:
categories:
  - android
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2016-8-4 17:46:51
desc:
keywords: Android Studio Device Monitor启动报错
---

![报错信息](data:image/png;base64,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)

<!--more-->

D:\xxx\sdk是我的sdk路径，于是到此目录下查看日志文件，描述如下：

```
!ENTRY org.eclipse.osgi 4 0 2016-08-04 11:21:12.845
!MESSAGE Application error
!STACK 1
java.io.IOException: The folder "C:\Users\Lison%20Liou\.android\monitor-workspace\.metadata" is read-only.
	at org.eclipse.core.runtime.internal.adaptor.BasicLocation.lock(BasicLocation.java:206)
	at org.eclipse.core.runtime.internal.adaptor.BasicLocation.set(BasicLocation.java:164)
	at org.eclipse.core.runtime.internal.adaptor.BasicLocation.set(BasicLocation.java:137)
	at com.android.ide.eclipse.monitor.MonitorApplication.start(MonitorApplication.java:53)
	at org.eclipse.equinox.internal.app.EclipseAppHandle.run(EclipseAppHandle.java:196)
	at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.runApplication(EclipseAppLauncher.java:110)
	at org.eclipse.core.runtime.internal.adaptor.EclipseAppLauncher.start(EclipseAppLauncher.java:79)
	at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:353)
	at org.eclipse.core.runtime.adaptor.EclipseStarter.run(EclipseStarter.java:180)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:606)
	at org.eclipse.equinox.launcher.Main.invokeFramework(Main.java:629)
	at org.eclipse.equinox.launcher.Main.basicRun(Main.java:584)
	at org.eclipse.equinox.launcher.Main.run(Main.java:1438)
```

错误信息说目录是只读的，但目录只有C:/Users/Lison Liou/.android，后面就没有了，于是干脆给目录加上Everyone用户的Full Control权限，结果还是不行。

后来在StackOverFlow上找到答案，[Android DDMS (Monitor) does not start if user profile contains a space in it](http://stackoverflow.com/questions/17424533/android-ddms-monitor-does-not-start-if-user-profile-contains-a-space-in-it)，说DDMS不会启动如果用户名包含空格，我的用户名的确包含空格，但是之前也是好用的，不解。

解决方法如下：以管理员身份运行cmd（必须），输入如下：

```
cd c:/users

mklink /d "Lison%20Liou" "Lison Liou"
```

将带空格的用户与正常用户名建立链接（软连接或者硬链接就不知道了），Lison Liou是我的计算机用户，成功后会提示：

**symbolic link created for Lison%20Liou <<===>> Lison Liou**

搞定。