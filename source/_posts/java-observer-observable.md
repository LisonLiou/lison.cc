---
title: java中的观察者/订阅者模式实践 - Observable、Observer
tags:
  - 设计模式
categories:
  - java
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2016-1-28 12:02:08
desc: java中的观察者/订阅者模式实践 – Observable、Observer
keywords: java设计模式,java观察者/订阅者模式,java Observable Observer 
---

这方面的资料搜索一下很多，但是自己一直没有实践过。今天用到了，做下记录。名词解释：

观察者，Observer
被观察者，Observable

举例，过年放烟花，烟花放在中间一直燃烧，就是被观察者；
而大家在周围看，就是观察者（哇~~~猴赛雷）
直接上代码：

<!--more-->

```
/**
	 * 作为被观察者也作为被订阅源
	 * 
	 * @author Administrator
	 * 
	 */
	static class Fireworks extends Observable {

		void setFire() {
			System.out.println("I am fired, check on that");
			
			//事件變化,通知所有觀察者
			super.setChanged();
			this.notifyObservers("qiu...qiu...qiu...");
		}
	}

	/**
	 * 觀察者
	 * 
	 * @author Administrator
	 * 
	 */
	static class People implements Observer {

		String name = "";

		public People(String name) {
			this.name = name;
		}

		public void update(Observable o, Object arg) {
			System.out.println(name + " watched: " + arg);
		}
	}
```

测试一下：

```
Fireworks f = new Fireworks();

		// 注冊觀察者
		f.addObserver(new People("XiaoMing"));
		f.addObserver(new People("XiaoQiang"));
		f.addObserver(new People("XiaoHong"));
		f.addObserver(new People("XiaoLi"));

		f.setFire();
```

输出：

I am fired, check on that
XiaoLi watched: qiu…qiu…qiu…
XiaoHong watched: qiu…qiu…qiu…
XiaoQiang watched: qiu…qiu…qiu…
XiaoMing watched: qiu…qiu…qiu…

之前使用的是直接new一个Observable对象，导致无法调用父类的setChanged方法。

见->[java 订阅者模式不执行update](http://www.oschina.net/question/1160643_2150413?fromerr=AhhXLMI3#tags_nav)