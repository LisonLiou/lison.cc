---
title: 学习基于链表的队列Queue实现java版
tags:
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
date: 2018-12-23 17:19:18
desc:
keywords: 队列java实现代码,队列基本操作代码,队列操作方法
---

队列Queue，先进先出 First In First Out (FIFO) 的线性表, 只允许在表的一端插入元素(队尾Rear), 表的另一端删除元素(队首Front)，基本操作有初始化队列，判断空，入队，出队，读取队首元素。

<!--more-->

```
/**
 * 单个节点
 * 
 * @author Lison-Liou
 *
 */
public class Node<E> {
	E data = null;
	Node<E> next;

	public Node() {
	}

	public Node(E data) {
		this.data = data;
	}

	public Node(E data, Node<E> next) {
		this.data = data;
		this.next = next;
	}
}
/**
 * 链表队列实现
 * @author Lison-Liou
 *
 * @param <E>
 */
public class MyQueue<E> {

	int size;
	Node<E> front, rear;

	public void initQueue() {
		front = rear = null;
	}

	public boolean isEmpty() {
		return size == 0;
	}

	public void enQueue(int data) {

		if (isEmpty()) {
			front = new Node(data);
			rear = front;
		} else {
			Node newRear = new Node(data);
			rear.next = newRear;
			rear = newRear;
		}

		size++;
	}

	public E deQueue() {

		if (!isEmpty()) {

			Node<E> f0 = front;
			Node<E> f = f0.next;
			front = f;

			size--;
			return f0.data;
		}
		return null;
	}

	public E frontQueue() {
		return front.data;
	}

	public void clear() {
		while (!isEmpty()) {
			deQueue();
		}
	}
}
//// 链表队列测试代码==========================================================================

		MyQueue<Integer> queue = new MyQueue<Integer>();
		queue.initQueue();

		System.out.println("入队列操作--------------------------");
		for (int i = 0; i < 7; i++) {
			int d = random.nextInt(i + 1);
			System.out.print(d + "\t");
			queue.enQueue(d);
		}

//		System.out.println("\r\n出队列操作--------------------------");
//		while (!queue.isEmpty()) {
//			System.err.print(queue.deQueue() + "\t");
//		}
		
		System.out.println("\r\n清空队列操作--------------------------");
		queue.clear();

		System.out.println("\r\nQUEUE SIZE: " + queue.size);
```

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAckAAABhCAMAAACK2ap0AAAAS1BMVEX///8AAAD//7ZmAAD/25Db//8AOpCQ2///tmZmtv86AgC2//8AAGYAADo6kNvbkDoAZrY6ADq2ZgD//9uQOgCQtpA6kJBmOpA6AGYoCV3+AAADU0lEQVR42u2d23KjMAxALUjS3Bqy7PX/v3TtCiKC6WY6wIzMntOmgMuohoMM9EEOAACwFiLjhtGmfiTRNUgAl0i/jPSb8sHDpC4w6R01Y76sUcY5aevodMgLk8/ahKR0i9iwOfQaMbki+gnpg0mnTJpUYWbSvuyOWtVxedj/ByuF8DCp3ybRTCaC6iMn3dKrMZMmWLftIRaTnpk2adsqVel25dnVI2I6ZfTe+MHAHjnpmWECjjPUzHVjLSb9IrbWOzKtjzyVroUnHgAAAAAAAOg5isjNUZzQyN3RcTUSOfmJ8/kJOr6/hd15xiEvHKeqf9Wxo27608VyFGf37Tx1gr7/uCXL10uYh8WZSXuqPkx66Y9eXafgJk48svY+Kfgt/jjPvlgszmzUpJ/+hNAc9sFNnHh1mslRwrfvP88zLxaL48Tkcv1pRNL14CVOyutPTP6Mo+5uvkmN48ik9Wc26X7rJU60OGVSByAdi+ZgcdyMrn1//AyLi8Q5Xi+fmKzq2xJ/weI4MblQfywRnMRpRZlI7ub9bYFByOI4Mdn3x9Hbg8ZZ8YJoZryxrhCnkcRh76U/KQmuFz9xOpMAAAAAAAAAADlia9QBKZSooqdroA5IoYjpE4M6ICUiypMq6oAUijwthTogBTK8T5pQ6oAUWAdEzFYHdUBKRRRdU6gDUiQyvlVSB6RITIyZpA5IgchkTlIHpEjUSRL0gDogAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABb5igi95Cq2mpJ/9iQuOUtUxOZtN2vqlpEA2U7Z7OxwCo010vUcB96iy2JvMVorSXuMFzNds5mY0HlKlT1SU/xl0xqlXHTZ7OtqMlXs7HAwuh5VzNfMJmcTOXk8bB/NtnIaXo2FlgYzRDNlvFd8RTylmFSShRifiy9bWczmc3GAmFtk69z0tJSXZrJ9m5Z/q/ZWDCpLG9S0+lrJtXlYW8mdakmX8zGwui6OKpLx70oUzU8VOQt+VWgBu1xJjeZz8biuaZNycT3CT3F7WEfddxNRd6Sz9BiJo8qaPqJx2ZjYXBdj1bf1nUlurFX+7zFJGpD9p+BqMp2NpOmVgSRq7H7wwveNkh3uOo3N68NUNWMeQBeoC7SVsDkVsDkVsDkJvgLAXcjURaKPhEAAAAASUVORK5CYII=)