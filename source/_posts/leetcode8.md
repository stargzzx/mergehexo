---
title: Offer 10- I. 斐波那契数列
date: 2020-08-06 13:01:44
categories:
- 算法
- leetcode
- 简单
tags:
- leetcode
---
今日算法题。 1 / 3。

<!-- more -->

- python3
- [Offer 10- I. 斐波那契数列](https://leetcode-cn.com/problems/fei-bo-na-qi-shu-lie-lcof/)

<br/>

# 题目描述

<br/>

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项。斐波那契数列的定义如下：

	F(0) = 0,   F(1) = 1
	F(N) = F(N - 1) + F(N - 2), 其中 N > 1.

斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 `1e9+7（1000000007）`，如计算初始结果为：`1000000008`，请返回 `1`。

 

示例 1：

	输入：n = 2
	输出：1

示例 2：

	输入：n = 5
	输出：5
 

提示：

- 0 <= n <= 100

<br/>

# 我的解法

<br/>

## 解法一

```python
class Solution:
    fib_dict = {}

    def fib(self, n: int) -> int:
        self.fib_dict.clear()
        self.fib_dict[0] = 0
        self.fib_dict[1] = 1
        return self.get_fib(n)
    
    def get_fib(self,n):
        if n in self.fib_dict:
            return self.fib_dict[n]
        else:
            self.fib_dict[n] = self.get_fib(n - 1) + self.get_fib(n - 2)
        return self.fib_dict[n] % 1000000007
```

