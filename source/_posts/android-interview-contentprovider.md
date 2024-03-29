---
title: 安卓基础 - 关于ContentProvider、ContentResolver
tags:
  - 安卓架构师面试题
categories:
  - 安卓面试
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2021-08-14 09:40:02
desc:
keywords: 安卓架构师面试题、安卓四大组件面试题ContentProvider、ContentResolver
---


<!--more-->

## 1. 什么是ContentProvider

​	ContentProvider是应用程序之间共享数据的接口。使用的时候首先自定义一个类继承自ContentProvider，然后覆写query、insert、update、delete等方法。因为其是安卓四大组件之一必须在AndroidManifest.xml文件中进行注册。把自己的数据通过uri形式共享出去。

```xml
<provider     
android:exported="true"     android:name="com.itheima.contenProvider.provider.PersonContentProvider"android:authorities="com.itheima.person" />

```

第三方可以通过ContentResolver来访问该Provider。

## 2. 为什么要用ContentProvider？它和sql的实现上有什么差别？

1. ContentProvider屏蔽了数据存储的细节，对外提供统一的数据访问接口。可以实现不同app之间的数据共享。
2. 更好的数据访问权限管理。ContentProvider可以对开发的数据进行权限设置，不同的URI可以对应不同的权限，只有符合权限要求的组件才能访问到ContentProvider的具体操作。
3. ContentProvider封装了跨进程共享的逻辑，我们只需要URI（Uniform Resource Identifier，即统一资源标识符）即可访问数据。由系统来管理ContentProvider的创建、生命周期、及访问的线程分配，简化我们在应用间的共享数据（进程间通信）的方式。我们只管通过ContentResolver访问ContentProvider所提示的数据接口，而不需要担心它所在的进程是启动还是未启动。

​		sql也有增删改查的方法，但是sql只能查询本应用下的数据库。而ContentProvider还可以去增删改查本地.xml等类型的文件。

## 3.运行在主线程的ContentProvider为什么不会影响主线程的UI操作？

​		ContentProvider的onCreate()是运行在UI线程的，而query()、insert()、delete()、update()都是在ContentProvider进程的线程池中被调用执行的，而不是进程的主线程。因为那些方法可能同时被多个线程调用，所以他们的数据同步问题，要在实现逻辑中处理好。
​		ContentProvider实现了进程间通信也是基于Binder机制的，所以会回到Binder的线程处理问题。并不是每个ContentProvider都会有一个线程池，而一个进程会共有一个线程池，其实就是Binder线程池。