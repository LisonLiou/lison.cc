---
title: 记java归并排序
tags:
  - 算法
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
date: 2019-5-15 19:22:00
desc:
keywords:
---

```
static int[] sort(int[] A, int low, int high) {

		if (low < high) {
			int mid = (low + high) / 2;
			sort(A, low, mid);
			sort(A, mid + 1, high);
			merge(A, low, mid, high);
		}

		return A;
	}
```

<!--more-->

```
	static void merge(int[] A, int low, int mid, int high) {
		int n1 = mid - low + 1, n2 = high - mid, i, j, k;
		int[] L = new int[high - low + 1], R = new int[high - low + 1];

		for (i = 0; i < n1; i++)
			L[i] = A[low + i];

		for (j = 0; j < n2; j++)
			R[j] = A[mid + j + 1];

		L[n1] = Integer.MAX_VALUE;
		R[n2] = Integer.MAX_VALUE;
		i = j = 0;

		for (k = low; k < high + 1; k++) {
			if (L[i] < R[j]) {
				A[k] = L[i];
				i++;
			} else {
				A[k] = R[j];
				j++;
			}
		}
	}
```

```
int[] arr = { 8, 3, 6, 4, 2 };

		System.out.println(Arrays.toString(arr));
		// System.err.println(Arrays.toString(sort(arr, 0, arr.length - 1)));
		// System.err.println(Arrays.toString(sort(arr, 0, arr.length - 1)));
		System.err.println(Arrays.toString(s(arr)));
```

使用分治法分而治之，时间复杂度O(nlogn)，空间复杂度O(n)