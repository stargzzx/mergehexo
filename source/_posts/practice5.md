---
title: 最长递增子序列
date: 2018-07-02 11:54:39
categories:
- practice
tags:
- practice
- algorithm
---
题目描述：
给定一个非排好序的数组，找到最长的递增子序列的长度。比如：[2,1,4,7,2,9] -> 2,4,7,9 返回 4 或 1,4,7,9 返回 4。最终为 4
要求时间复杂度为 O(n * log(n))
<!-- more -->
我们看见 log(n) 可以想到排序以及查找中 log(n) 的算法，但这道题目显然和排序无关，因为我们要维护好原来的序列，所以我们首先想到的是二分查法。
可以先看我的博文，在代码中我也要引用上述博文中的代码。
[二分法详解](https://benpaodewoniu.github.io/2018/06/30/practice4/)
## 原理
原理和 KMP 类似，如果你能知道 KMP 或者 BP 算法，那就应该很容易理解下面的原理。
## 代码
暂时还没有写好，有点BUG
{% codeblock %}

def max_len(arr):
	i = 0
	l = []
	l.append(1)
	tmp = [0 for k in range(0,len(arr))]
	tmp[0] = arr[0]
	for j in range(1,len(arr)):
		index = gainFirst(tmp[:(i+1)],arr[j])
		print(index)
		if index <= i:
			i = index	 + 1
		else:
			i += 1
		tmp[i] = arr[j]	
		l.append(i + 1)
	return l

{% endcodeblock %}
## 测试
{% codeblock %}

b = [1,2,3,4,5,6]
print(max_len(b))

{% endcodeblock %}





