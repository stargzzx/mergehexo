---
title: leetcode 2
date: 2019-02-14 16:45:01
categories:
- practice
- 算法练习
tags:
- leetcode
- practice
- algorithm
---
这是刷的第 2 个 leetcode 题。

<!-- more -->

## 介绍

Title：
Reverse Integer    
Acceptance：
25.1%
Difficulty:
Easy
[地址](https://leetcode.com/problems/reverse-integer/)
解决语言：
python3

## Description

Given a 32-bit signed integer, reverse digits of an integer.

Example 1:

	Input: 123
	Output: 321
	
Example 2:

	Input: -123
	Output: -321
	
Example 3:

	Input: 120
	Output: 21

Note:

Assume we are dealing with an environment which could only store integers within the 32-bit signed integer range: [−2^31,  2^31 − 1]. For the purpose of this problem, assume that your function returns 0 when the reversed integer overflows.

## code

```python
class Solution:
    def reverse(self, x: 'int') -> 'int':
		# lock 是一个判断锁
		# 使用场景如下，对于 10900 这种数，因为 1 和 9 之间 有 0 ，所以，这个 0 不能省
		# 但是 9 之后的 0 要省
		# 所以增加一个锁用来判断那个 0 是否需要省
        lock = 0
		# 用来返回是负数还是正数
        if x > 0:
            b = 1
        else:
            b = -1
        x = abs(x)
        a:int = 0
		# 和之前用 list 不同的是，这次直接用了数字存储
		# 相较于 list 来说，操作更简单，运行更快，后期不需要拼接
        while(True):
			# 当 x 不是 0 的时候
            if(x != 0):
                if(x % 10 != 0):
                    if(a == 0):
                        a = x % 10
                    else:
                        a = a * 10 + (x % 10)
                    lock = 1
                    x = x // 10
                elif(x % 10 == 0 and lock == 1):
                    a = a * 10
                    x = x // 10
                else:
                    x = x // 10
            else:
                break
		# 这个是用来判断 int32 位的
        if a * b > 2147483647:
            return 0
        elif a * b < -2147483648:
            return 0
        else:
            return a * b
```

## Result

Runtime: 52 ms, faster than 89.25% of Python3 online submissions for Reverse Integer.
Memory Usage: 12.7 MB, less than 100.00% of Python3 online submissions for Reverse Integer.

## other code
```python
class Solution:
    # @return an integer
    def reverse(self, x):
        result = 0

        if x < 0:
            symbol = -1
            x = -x
        else:
            symbol = 1

        while x:
            result = result * 10 + x % 10
            x //= 10

        return 0 if result > pow(2, 31) else result * symbol
```










