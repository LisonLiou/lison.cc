---
title: java byte[] 字节数组按份分割拆分成多个数组
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
date: 2016-5-24 17:31:44
desc: 将byte数组拆分成新的多个byte数组,将一个大的byte[]数组拆分多个byte数组,byte[]分割数组
keywords: java byte[]拆分,byte[]分组,java拆分字节数组
---

java byte[] split

简单的字节数组分割方法，输入要拆分的数组及要按几个拆成一份即可，返回二维数组byte[]，做记录直接上代码了。

<!--more-->

```
/**
	 * 拆分byte数组
	 * 
	 * @param bytes
	 *            要拆分的数组
	 * @param copies
	 *            要按几个组成一份
	 * @return
	 */
	static byte[][] split_bytes(byte[] bytes, int copies) {

		double split_length = Double.parseDouble(copies + "");

		int array_length = (int) Math.ceil(bytes.length / split_length);
		byte[][] result = new byte[array_length][];

		int from, to;

		for (int i = 0; i < array_length; i++) {

			from = (int) (i * split_length);
			to = (int) (from + split_length);

			if (to > bytes.length)
				to = bytes.length;

			result[i] = Arrays.copyOfRange(bytes, from, to);
		}

		return result;
	}
```

测试代码：

```
byte[] sum = { 23, 4, 23, 42, 34, 2, 34, 2, 34, 2, 54, 3, 4, 56, 4, 7, 56, 7, 8, 5, 15, 2, 34, 2, 41, 2, 32, 3,
				3, 3, 33 };
		
		//按四个分成一组
		byte[][] bytes = split_bytes(sum, 4);

		System.out.println(bytes.length);

		System.out.print("\r\n\r\n==========================================\r\n");

		for (int i = 0; i < bytes.length; i++) {
			for (int j = 0; j < bytes[i].length; j++) {
				System.out.print(bytes[i][j] + " ");
			}

			System.out.println("");
		}
```

输出结果：

8
==========================================
23 4 23 42
34 2 34 2
34 2 54 3
4 56 4 7
56 7 8 5
15 2 34 2
41 2 32 3
3 3 33