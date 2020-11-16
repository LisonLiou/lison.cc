---
title: Java拾遗：多线程中的生产者消费者问题 – 线程的同步
tags:
  - 多线程
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
date: 2020-9-18 20:10:50
desc: 使用这个机制，程序能够非常简单的协调洗刷线程和烘干线程，而且并不需要了解这些线程的身份。每当执行一项工作，使得另一个线程能够开始工作，就通知对象drainingBoard(调用notify())；每当由于盘架空或满而不能继续工作时，就等待对象drainingBoard(调用wait())。
keywords: 多线程共享数据,线程同步的实现方式,java多线程
---

> 为了完成多个任务，常创建多个线程，它们可能毫不相关，但有时它们完成的任务在某种程度上有一定的关系，此时就需要线程之间有一些交互。在Java中，使用一对方法wait()和notify()/notifyAll()实现线程的交互。

<!--more-->

### 同步问题的提出

操作系统中的生产者消费者问题，就是一个经典的同步问题。举一个例子，有两个人，一个人在刷盘子，另一个人在烘干。这两个人各自代表一个线程，他们之间有一个共享的对象 — 盘架，刷好而等待烘干的盘子放在盘架上。两个人在没有事做事都愿意歇着。显然，盘架上有刷好的盘子时，烘干的人才能开始工作；而如果刷盘子的人刷的太快，刷好的盘子占满了盘架时，他就不能再继续工作了，而要等到盘架上有空位置才行。

这个示例要说明的问题是，生产者生产一个产品后就放入共享对象中，而不管共享对象中是否有产品。消费者从共享对象中取用产品，但不检测是否已经取过。

若共享对象中只能存放一个数据，可能出现以下问题（线程不同步的情况下）：

– 生产者比消费者快时，消费者会漏掉一些数据没有取到。
– 消费者比生产者快时，消费者取相同的数据。

在java语言中，可以用wait()和notify()/notifyAll()方法来协调线程间的运行速度关系，这些方法都定义在java.lang.Object类中。

### 解决方法

为了解决线程运行速度问题，**Java提供了一种建立在对象实例之上的交互方法。\**Java中的每个对象实例都有两个线程队列和他相连\**。\**第一个用来排列等待锁定标志的线程。第二个则用来实现wait()和notify()的交互机制\**。**

类java.lang.Object中定义了三个方法wait()和notify()/notifyAll()。

wait方法导致当前的线程等待，同时会让当先线程释放其所持有的“对象互斥锁”，进入wait队列（等待队列）；而notify()/notifyAll()方法的作用是唤醒一个或所有正在等待队列中等待的线程，并将它（们）移入用一个“对象互斥锁”队列。notify()/notifyAll()方法和wait()方法都只能在被声明为synchronized的方法或代码中调用。方法notify()最多只能释放等待队列中的第一个线程，如果有多个线程在等待，则其他的线程将继续留在队列中。notifyAll()方法能够释放所有等待线程。

再来看看前面刷盘子的例子。线程t1代表刷盘子，线程t2代表烘干，它们都有对盘架drainingBoard的访问权。假设线程t2（烘干线程）想要进行烘干工作，而此时盘架时空的，则应表示如下：

```
if(drainingBoard.isEmpty())
	drainingBoard.wait(); //盘架空时则等待
```


当线程t2执行了wait()调用后，它不可以再执行，并加入到对象drainingBoard的等待队列中。在有线程将它从这个队列释放之前，它不能再次运行。

那么，烘干线程怎样才能重新运行呢？这应该有洗刷线程t1来通知它已经有工作可以做了，运行drainingBoard的notify调用可以做到这一点：

``` 
drainingBoard.addItem(); //放入一个盘子
drainingBoard.notify();
```


此时，drainingBoard的等待队列中第一个阻塞线程由队列中释放出来，并可重新参加运行的竞争。

注意，在这里使用notify调用时，没有考虑是否有正在等待的线程。事实上，应该只有在增加盘子后使得盘架不再空时才执行这个调用。如果等待队列中没有阻塞线程时调用了方法notify()，则这个调用不做任何工作。notify()调用不会被保留到以后再发生效用。

使用这个机制，程序能够非常简单的协调洗刷线程和烘干线程，而且并不需要了解这些线程的身份。每当执行一项工作，使得另一个线程能够开始工作，就通知对象drainingBoard(调用notify())；每当由于盘架空或满而不能继续工作时，就等待对象drainingBoard(调用wait())。

在调用一个对象的wait(),notify()/notifyAll()时，必须首先持有该对象的锁定标志，因此这些方法必须在同步程序块中调用。这样，应该将代码改写如下：

```
synchronized(drainingBoard) {
 if(drainingBoard.isEmpty())
  	drainingBoard.wait();
 }
```


和
```
 synchronized(drainingBoard) {
	drainingBoard.addItem();
	drainingBoard.notify();
}
```

附上源码：

> DrainingBoard.java

```
public class DrainingBoard {
 private ArrayList<String> plates;
 private static int MAX_ITEM_COUNT = 10;

 public DrainingBoard() {
 	plates = new ArrayList<>();
 }

 public void addItem() {
 	if (plates.size() >= MAX_ITEM_COUNT)
 		return;
 	
 	plates.add(“P” + plates.size());
 }

 public void minusItem() {
 	if (!isEmpty())
 		plates.remove(0);
 }

 public boolean isEmpty() {
 	return plates.size() == 0;
 }

 public int getSize() {
 	return plates.size();
 }

 public boolean isFull() {
 	return plates.size() >= MAX_ITEM_COUNT;
	 }
 }
```

> main.java

```
 DrainingBoard drainingBoard = new DrainingBoard();

 Thread t1 = new Thread() {

 @Override
 public void run() {
 	while (true) {
 	synchronized (drainingBoard) {
 	try {

 		if (drainingBoard.isFull()) {
 			System.out.println(“Full wait()”);> drainingBoard.wait(); //t1进入等待（drainingBoard）的队列，资源达到上限
 		} else {
 			drainingBoard.addItem();
 			System.out.println(“Add notify()” + drainingBoard.getSize());
 			drainingBoard.notify(); //通知等待（drainingBoard）队列中的第一个线程可以出队了
 		}

 	} catch (Exception e) {
 		e.printStackTrace();
 	}
 }
 }
 }
 };

 Thread t2 = new Thread() {
 @Override
 public void run() {
 	while (true) {
 		synchronized (drainingBoard) {
 		try {
 			if (drainingBoard.isEmpty()) {

 				System.out.println(“Empty wait()”); drainingBoard.wait(); //t2进入等待（drainingBoard）的队列（资源枯竭），知道有人notify他才可以出队

 			} else {
 				System.out.println(“Have minus()” + drainingBoard.getSize());
 				drainingBoard.minusItem();
 				drainingBoard.notify(); //通知等待（drainingBoard）的队列中的第一个线程出队（释放）
 			}
		 } catch (InterruptedException e) {
 			e.printStackTrace();
 		}
 	}
 }
 };
 };

 // t1.setPriority(Thread.MIN_PRIORITY);
 // t2.setPriority(Thread.MAX_PRIORITY);

 t1.start();
 t2.start();

部分运行结果（因机器而异，并不完全相同）：

> Add notify()1
> Add notify()2
> Add notify()3
> Have minus()3
> Have minus()2
> Have minus()1
> Empty wait()
> Add notify()1
> Add notify()2
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Add notify()8
> Add notify()9
> Add notify()10
> Full wait()
> Have minus()10
> Have minus()9
> Have minus()8
> Have minus()7
> Have minus()6
> Have minus()5
> Have minus()4
> Have minus()3
> Have minus()2
> Have minus()1
> Empty wait()
> Add notify()1
> Add notify()2
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Add notify()8
> Add notify()9
> Add notify()10
> Have minus()10
> Have minus()9
> Have minus()8
> Have minus()7
> Have minus()6
> Have minus()5
> Have minus()4
> Have minus()3
> Have minus()2
> Have minus()1
> Empty wait()
> Add notify()1
> Add notify()2
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Add notify()8
> Add notify()9
> Add notify()10
> Full wait()
> Have minus()10
> Have minus()9
> Have minus()8
> Have minus()7
> Have minus()6
> Have minus()5
> Have minus()4
> Have minus()3
> Have minus()2
> Have minus()1
> Empty wait()
> Add notify()1
> Add notify()2
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Add notify()8
> Add notify()9
> Add notify()10
> Full wait()
> Have minus()10
> Have minus()9
> Have minus()8
> Have minus()7
> Have minus()6
> Have minus()5
> Have minus()4
> Have minus()3
> Have minus()2
> Have minus()1
> Empty wait()
> Add notify()1
> Add notify()2
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Add notify()8
> Add notify()9
> Add notify()10
> Full wait()
> Have minus()10
> Have minus()9
> Have minus()8
> Add notify()8
> Add notify()9
> Add notify()10
> Full wait()
> Have minus()10
> Have minus()9
> Have minus()8
> Have minus()7
> Have minus()6
> Have minus()5
> Have minus()4
> Have minus()3
> Have minus()2
> Have minus()1
> Empty wait()
> Add notify()1
> Add notify()2
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Have minus()7
> Have minus()6
> Have minus()5
> Have minus()4
> Have minus()3
> Have minus()2
> Have minus()1
> Empty wait()
> Add notify()1
> Add notify()2
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Add notify()8
> Add notify()9
> Add notify()10
> Full wait()
> Have minus()10
> Have minus()9
> Have minus()8
> Have minus()7
> Have minus()6
> Have minus()5
> Have minus()4
> Have minus()3
> Add notify()3
> Add notify()4
> Add notify()5
> Add notify()6
> Add notify()7
> Add notify()8
> Add notify()9
> Add notify()10
> Full wait()

文章讲解摘自《Java语言程序设计（一）》多线程章节，文章中没有给出详细代码，害得我亲手撸了一遍，不过也巩固了科学技术知识，提高了加班生产力。

其中的对线程设置优先级的地方已注释，怎么切换好像都看不出太不一样的地方。
```