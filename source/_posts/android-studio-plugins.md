---
title: Android Studio优秀插件最佳实践，不定期更新中
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
date: 2020-4-15 19:47:39
desc:
keywords: Android Studio必备插件,Android提升开发效率插件
---

本来Intellij全家桶的东西已经够强大了，然而还有这么多优秀的插件，本文手记笔者开发过程中接触使用的插件，在此作记录，插件排名不分先后（当然eclipse也很经典）

<!--more-->

- [SVG2VectorDrawable](https://plugins.jetbrains.com/plugin/8103-svg2vectordrawable) 字面意思可以看明白，svg转向量图Drawable对象，APP图片瘦身必备步骤，之前都是使用在线工具直接生成各种大小的Drawable对象，本质还是图片，这个工具将图片直接转为Vector。典型的使用场景是比如我们app底部的Tab选项卡使用的那一类小图标，形式比较固定，就是一个默认的icon，一个默认颜色，按下时变成其他颜色。关于Vetor对象的图标的高级使用方法详见 -> https://gist.github.com/pgq10240817/18acdb1c84bf9b142793562de1dcee0f。
- [Lifecycle Sorter](https://plugins.jetbrains.com/plugin/7742-lifecycle-sorter)，这个翻译成生命周期整理器？没错，就是自动将Activity，Fragment的override方法比如onCreate，onResume等方法依次按照生命周期顺序从上到下重新排列，不用每次都费力去差sdk了吧。安装完成点击Code->Sort Lifecycle Methods。不过如果你都能记得住并且此时是一脸不屑的话，na当我没说。[传送门这里—>](https://plugins.jetbrains.com/plugin/7742-lifecycle-sorter)
- [InnerBuilder](https://plugins.jetbrains.com/plugin/7354-innerbuilder)，将Bean转化为构造器与链式调用模式的代码，将原始的getter setter方式改造为Builder加链式调用，例如：User user = new User.Builder().firstName(“Lison”).lastName(“Liou”).age(32).phoneNo(“15066796811”).build();
  而这之前通常的写法是：User user=new User(); user.setFirstName(“Lison”) … [传送门](https://plugins.jetbrains.com/plugin/7354-innerbuilder)
- [Parcelable Code Generator](https://plugins.jetbrains.com/plugin/7332-android-parcelable-code-generator)，要在Intent中传递序列化对象，一种方法是实现Serializeble接口，另外一种就是实现Parcelable接口；Parcelable的方式效率比较高，但需要重写一堆方法，本插件就是为此而生，自动生成describeContents，writeToParcelable等方法。[传送门](https://plugins.jetbrains.com/plugin/7332-android-parcelable-code-generator)
- [IdeaVim](https://plugins.jetbrains.com/plugin/164-ideavim/) 这个不用多做介绍，懂的自然懂，Android Studio的Vim模拟器，反正我在几乎所有的IDE上都装了Vim插件，Android Studio, Pycharm, VS2019，VScode bla，[传送门](https://plugins.jetbrains.com/plugin/164-ideavim/)
- [lombok](https://plugins.jetbrains.com/plugin/6317-lombok/versions) 帮助你减少重复代码编写的工作，比如重复的编写getter setter代码，重复的使用Builder插件生成代码（InnerBuilder没别的意思），简单化注解同步方法等等。使用很酷，很提高效率。需要注意的是有些版本的插件与当前Android Studio不兼容，需要自己尝试下。[传门送。](https://plugins.jetbrains.com/plugin/6317-lombok/versions) [注解列表介绍。](https://projectlombok.org/features/all)

 

今天先放三个，后面慢慢更新。。。

有句流行语怎么说来，乾坤未定，你我皆是黑马。虽然不再高考，但也没到认命的时候把。