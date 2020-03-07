---
title: 寻找奇数出现
date: 2018-08-07 23:07:06
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：

给定一个整形数组 arr ，其中只有一个数出现了奇数次，其他的数都出现了偶数次，请打印这个数，要求时间复杂度为O(N),额外空间复杂度为O(1)

<!-- more -->

## 原理

	假设有一个数 n
	n 与 0 异或的结果为 n
	n 与 n 异或的结果为 0
	且
	异或运算满足交换律
	异或运算满足结合律
	即
	0 ^ A ^ C ^ B ^ A ^ C = 0 ^ A ^ A ^ C ^ C ^ B
	= 0 ^ B
	即给定数组，设 a = 0
	让 a 与第一个数进行异或运算，得出来得数和下一个数进行异或运算
	最终
	得到的数就是所要找的数
	
## 代码
{% codeblock %}
arr = [1,2,3,2,1]
a = 0
for i in range(len(arr)):
    a = a ^ arr[i]
print(a)
{% endcodeblock %}

