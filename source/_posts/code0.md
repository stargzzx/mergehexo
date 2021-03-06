---
title: 下标问题
date: 2020-07-02 23:03:26
categories:
- 算法
- 基础
tags:
- 算法
---
数组下标一直把我搞的头晕脑转，所以，写一篇总结一下，我所知道的关于下标的问题。

<!-- more -->

<br/>

# 基础

<br/>

- 正序排列下标是从 `0` 开始
- 倒序排列下标是从 `1` 开始

<br/>

# 下标增长

<br/>

假设有一个数组 list

	l = len(list)

则最高增长下标不成超过 `l`

	i++
	i < l

下面举一个双端数组下标问题

## 圆圈中最后剩下的数字 「剑指 offer」

0,1,,n-1这n个数字排成一个圆圈，从数字0开始，每次从这个圆圈里删除第m个数字。求出这个圆圈里剩下的最后一个数字。

例如，0、1、2、3、4这5个数字组成一个圆圈，从数字0开始每次删除第3个数字，则删除的前4个数字依次是2、0、4、1，因此最后剩下的数字是3。

示例 1：

	输入: n = 5, m = 3
	输出: 3

示例 2：

	输入: n = 10, m = 17
	输出: 2

限制：

	1 <= n <= 10^5
	1 <= m <= 10^6

解答思路:

```python
class Solution:
    def lastRemaining(self, n: int, m: int) -> int:
        i, a = 0, list(range(n))
        while len(a) > 1:
            i = (i + m - 1) % len(a)
            a.pop(i)
        return a[0]
```

这个算法把很多经典问题都表现出来了。

- 正序 增加 m ，也就是 i + m -1
- 当在此刻下标 i  `pop()` 出去的时候，再次增加 m 还是原来的 i + m -1
	- 长度为 8，每次去除从当前位置数第 3 位，从 0 开始，第一次的下标去除的是 2 ，由于 pop 出去了，所以，原来的下标 3 变成了 2 ，也就是从当前下标数后 3 位，也就是 i + m -1

除此之外，将一个长链数组变成一个圆，通常用取余来解决

<br/>

# 对应关系

<br/>

数组又或者是链表，其头尾是什么对应的呢？

- 正序的下标是从 `0` 开始，倒序的下标是从 `1` 开始

## 获取链表中倒数第 K 个节点 「剑指 offer」

使用两个指针，第一个指针先走 k - 1 步，然后两个指针一起走。当第一个指针走到尾节点的时候，第二个指针指向的就是倒数第 k 个节点。

首先，我们先明确一下对应关系，倒序的第 `k` 个，也就是开始是 `1` ，结束是 `k - 1`，换成正序就是开始是 `0` ，结束是 `k - 1`

因为第一个指针和第二个指针之间相距 k 个节点，所以当最后一个指针走到尾指针的时候，另外一个指针，就是倒数第 `k` 个。

<br/>

# 数组变种

<br/>

## 数组变成一个圆

- 通常用取余来操作

获得该位的后三位坐标

```python
i = (i + m) % len(list)
```

- 相关的代码可以看上面的 圆圈中最后剩下的数字 「剑指 offer」