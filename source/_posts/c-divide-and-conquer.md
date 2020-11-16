---
title: C语言假币问题（分治法）
tags:
  - 算法
categories:
  - c
author: Lison
img: ''
top: false
cover: false
coverImg: ''
toc: false
mathjax: false
summary: ''
date: 2019-4-13 17:39:58
desc:
keywords:
---

分治法，o(nlogn)

```
/**
	 * 【假币问题】 有n枚硬币，其中有一枚是假币，已知假币的重量较轻。现只有一个天平，要求用尽量少的比较次数找出这枚硬币。
	 *
	 * 【分析问题】 将n枚硬币分成相等的两部分： 1.当n为偶数时，将前后两部分，即 1...n/2
	 * 和n/2+1...0，放在天平的两端，较轻的一端里有假币，继续在较轻的这部分硬币中用同样的方法找出假币： 2.当n为奇数时，将前后两部分，即
	 * 1...(n-1)/2 和 (n+1)/2+1...0，放在天平的两端，较轻的一端里有假币，继续在较轻的这部分硬币中用同样的方法找出假币；
	 * 若两端重量相等，则中间的硬币，即第 (n+1)/2 枚硬币是假币。
	 *
	 * 【调用方式】 int cs[] = {2, 2, 2, 2, 2, 2, 2, 2, 2, 2, 1, 2,}
	 * getCounterfeitCoint(cs, cs[0], cs[cs.length-1])
	 *
	 * @param coins
	 * @param first
	 * @param last
	 * @return
	 */
	static int getCounterfeitCoin(int coins[], int first, int last) {

		int firstSum = 0, lastSum = 0, i;

		if (first == last - 1) { // 只剩两枚
			if (coins[first] < coins[last])
				return first;

			return last;
		}

		if ((last - first + 1) % 2 == 0) { // 偶数枚
			for (i = first; i < (first + last) / 2; i++)
				firstSum += coins[i];

			for (i = first + (last - first) / 2 + 1; i < last + 1; i++)
				lastSum += coins[i];

			if (firstSum < lastSum)
				return getCounterfeitCoin(coins, first, first + (last - first) / 2);
			else
				return getCounterfeitCoin(coins, first + (last - first) / 2 + 1, last);

		} else { // 奇数枚

			for (i = first; i < first + (last - first) / 2; i++)
				firstSum += coins[i];

			for (i = first + (last - first) / 2 + 1; i < last + 1; i++)
				lastSum += coins[i];

			if (firstSum < lastSum)
				return getCounterfeitCoin(coins, first, first + (last - first) / 2 - 1);
			else if (firstSum > lastSum)
				return getCounterfeitCoin(coins, first + (last - first) / 2 - 1, last);
			else
				return (first + last) / 2;

		}
	}
```

<!--more-->