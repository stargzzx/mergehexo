---
title: Offer 16. 数值的整数次方
date: 2020-07-29 14:13:33
categories:
- 算法
- leetcode
- 中等
tags:
- leetcode
---
今天的算法任务。 1 / 3。

<!-- more -->

- python3

<br/>

# 题目描述

<br/>

- [剑指 Offer 16. 数值的整数次方](https://leetcode-cn.com/problems/shu-zhi-de-zheng-shu-ci-fang-lcof/)

实现函数double Power(double base, int exponent)，求base的exponent次方。不得使用库函数，同时不需要考虑大数问题。

示例 1:

	输入: 2.00000, 10
	输出: 1024.00000

示例 2:

	输入: 2.10000, 3
	输出: 9.26100

示例 3:

	输入: 2.00000, -2
	输出: 0.25000
	解释: 2-2 = 1/22 = 1/4 = 0.25
 

说明:

- -100.0 < x < 100.0
- n 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 。

<br/>

# 我的解法

<br/>

## 回归 + 记忆法

- 执行用时 44 ms ，击败 43.90% 的用户
- 内存消耗 13.7 MB ，击败了 100% 的用户

如果直接使用暴力破解，很容易超时，所以，这里用了记忆法。你可以看我下面的博文，进行加强学习。

- [算法 | 斐波那契与记忆性](https://benpaodewoniu.github.io/2020/06/16/algorithm37/)

```python
class Solution:

    pow_map = {}

    def myPow(self, x: float, n: int) -> float:
        self.pow_map.clear()
        self.pow_map[1] = x
        self.pow_map[2] = x * x
        if n == 0:
            return 1
        elif n < 0:
            return (1 / self.getNum(x,abs(n)))
        else:
            return (self.getNum(x,n))
    
    def getNum(self,x:float,n:int):
        if n not in self.pow_map:
            if n % 2 != 0:
                self.pow_map[n] = self.getNum(x,(n - 1) / 2) * self.getNum(x,(n-1) / 2) * x
            else:
                self.pow_map[n] = self.getNum(x,n / 2) * self.getNum(x,n / 2)
        return self.pow_map[n]
```