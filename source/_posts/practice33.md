---
title: 比较练习
date: 2018-08-05 14:58:31
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：

给定两个32位整数 a 和 b，返回 a 和 b 中较大的，但是不能用任何比较判断。

<!-- more -->

## 原理

首先我们要了解

	A 位操作符 B = C
	0 & 1 = 0
	用 C 来替代 A 得：
	0 ^ 1 = 1
	或者
	1 & 1 = 1
	用 C 来替代 A 得：
	1 ^ 1 = 0
	也就是说用 &  B 得到的数值，再用来 ^ B得到的数值，两个数值加起来是 1，即其中一个是 1，另一个是 0。
	
	所以，让 (a - b) >> 31 得到它的正负值，然后和 & 1 运算得 I，得到的数再和 ^ 1 运算，得 J
	返回 I * a + J * b
	也就是说，如果 a - b > 0.则返回 b,否则返回 a

## 代码

{% codeblock %}
def test(a,b):
    number = a - b
    f = number >> 31
    i = f & 1
    print(i)
    j = i ^ 1
    print(j)
    return  i * a + j * b
    
print(test(8,7))
{% endcodeblock %}

