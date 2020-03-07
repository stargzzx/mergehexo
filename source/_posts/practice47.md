---
title: leetcode 3
date: 2019-02-15 14:59:32
categories:
- practice
tags:
- leetcode
- practice
- algorithm
---
这是刷的第 3 个 leetcode 题。

<!-- more -->

## 介绍

Title：
Reverse Integer    
Acceptance：
41.6%
Difficulty:
Easy
[地址](https://leetcode.com/problems/palindrome-number/submissions/)
解决语言：
python3

## Description

Determine whether an integer is a palindrome. An integer is a palindrome when it reads the same backward as forward.
Example 1:

	Input: 121
	Output: true
	
Example 2:

	Input: -121
	Output: false
	Explanation: From left to right, it reads -121. From right to left, it becomes 121-. Therefore it is not a palindrome.

Example 3:

	Input: 10
	Output: false
	Explanation: Reads 01 from right to left. Therefore it is not a palindrome.

## code
{% codeblock %}
class Solution:
    def isPalindrome(self, x: 'int') -> 'bool':
        b = x
        if(x < 0):
            return False
        a = 0
        while(x):
            a = a * 10 + x % 10
            x = x // 10
        if(a == b):
            return True
        else:
            return False		
{% endcodeblock %}

这道题目和我上次写的 leetcode 2 原理差不多，在此不再详述。

## Result

Runtime: 240 ms, faster than 90.23% of Python3 online submissions for Palindrome Number.
Memory Usage: 12.8 MB, less than 100.00% of Python3 online submissions for Palindrome Number.






