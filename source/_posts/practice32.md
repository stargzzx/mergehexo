---
title: 交换练习
date: 2018-08-03 22:50:28
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：

如何不用任何的额外变量交换两个整数的值？

<!-- more -->

## 原理

只需要三行代码就可以解决，是不是超级酷炫。

	a = a ^ b
	b = a ^ b
	a = a ^ b
	
具体原因如下：

	a = a0;b = b0
	a = a ^ b # a = a0 ^ b0,b = b0
	b = a ^ b # a = a0 ^ b0,b = a0 ^ b0 ^ b0 = a0
	a = a ^ b # a = a0 ^ b0 ^ a0 = b0,b = a0
	
对于 ^ 异或的规则是
	
	1 ^ 1 = 0
	1 ^ 0 = 1
	0 ^ 1 = 1
	0 ^ 0 = 0
	
当然，在 python 中，交换两个数只需要一行代码：

	a,b = b,a
	
## 代码

这个简单当然不写了



