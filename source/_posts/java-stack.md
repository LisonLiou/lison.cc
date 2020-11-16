---
title: 学习基于链表的栈Stack实现java版
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
date: 2018-12-23 17:16:55
desc:
keywords: java栈实现代码,栈链表实现代码,栈的基本操作
---

“栈” 遵循先进后出的原则，基本操作有：初始化栈，判断栈空，入栈出栈，读取栈顶元素，代码实现如下

<!--more-->

```
/**
 * 单个节点
 * 
 * @author Lison-Liou
 *
 */
public class Node {
	int data = 0;
	Node next;

	public Node() {
	}

	public Node(int data) {
		this.data = data;
	}

	public Node(int data, Node next) {
		this.data = data;
		this.next = next;
	}
}
/**
 * 后进先出 LIFO LAST IN FIRST OUT
 * 
 * @author Lison-Liou
 *
 */
public class MyStack {

	Node top;
	int size;

	public void initStack() {
		top = null;
	}

	public boolean isEmpty() {
		return size == 0;
	}

	public void push(int x) {
		top = new Node(x, top);
		size++;
	}

	public int pop() {

		Node t = top;
		top = top.next;
		size--;
		return t.data;
	}

	public int top() {
		return top.data;
	}
}
```

测试代码

```
MyStack stack = new MyStack();
		stack.initStack();

		System.out.println("入栈操作--------------------------");
		for (int i = 0; i < 6; i++) {
			int d = random.nextInt(10);
			System.out.print(d + "\t");

			stack.push(d);
		}

		System.out.println("\r\n出栈操作--------------------------");
		while (!stack.isEmpty())
			System.err.print(stack.pop() + "\t");
```

输出结果

![](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAX4AAABZCAMAAAAO2r/0AAAAaVBMVEX///8AAACgoKD/AADb/////7YAOpBmAAD/25D/tmb/ADr/2/+Q2/8AADo6AAD/ZrYAZrb/kDrbkDr//9v/tv9mtv//kNs6kNv/OgCQOgD/ZgC2ZgD/OpC2////AGYAAGY6ADr/tpCQtpDPzvfZAAACf0lEQVR42u3b3XLaMBCGYW0VEjuF8GNIwk9I2/u/yGoJZsuAjyTZrvI+GVsOB9+YZZGczMj9wIAcAADAtyJi13I6tYMT5drXkIVV1upuA+XPTq7LH1x1v13zGeQg5yHQ0+X6Qmj/fKy0cjnpcPk09KD8JlP5rxpf/v2xlydPYXx8+AYX/bDyW7srEfuV7s9HbNaxwdZcR/kzstq2gz3qB9b9PPlkIXa2534rOd2f081Djy2150vKn4sV1Up/GkSP89eApRcAAAD43+n/99L8h68WkTcXby8ym5aXs5Jg4+5ptov46od7rF7i679ahGM2LS6nrVHHy5GarVZ+9fnhEqheNq7EnMnTJlPzV7+mepuz6Zje7thyVo8PyZvfMvazP2nuU7/sxeWsdA1J2/ymDqVfJGqTWpfwAnN0beyYN2LpvJMoqhZth/JyOmafvb4Wv6q8WXzkB7nQobgctb+NqpLN1xo1mnc7tpyORbbZWsdG/10RX/1mK+rzo7Act/9KAQAAAAD0TtjZOBRpD3Y2DsDKz87GAVj52dk4BGmxs3Eo4tjZeHPRH3HsbByG2Imdjf0TO7GzsX9iJ3Y29k8MOxsHwM5GAAAAoHxLH6xdvJ33zz8LzKm9968u1sSfHNwddYL7XM7DUWCO1qay+kdp3uf3P5q1S6EqL6d518ovjy5ad5svD24sb3dsOZVWrPJ6ztP8S2/hcfTLXlqONuzu+bdf52v+VHNbHWJKy6lD6ef2NYpv/myzT+1DfGk5X/NOpadY3SG7JDeqIaXlTPxrqvbcHbonpZG82/Hl6PqRYu7pCNl5748pJjavjqXlnB5N0sz8BwcAAAAAAAAAQBZ/AT9+Ic6/5z8FAAAAAElFTkSuQmCC)