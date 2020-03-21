---
title: leetcode 4
date: 2019-02-16 10:39:33
categories:
- practice
-算法练习
tags:
- leetcode
- practice
- algorithm
---
这是刷的第 4 个 leetcode 题。

<!-- more -->

## 介绍

Title：
Roman to Integer    
Acceptance：
51.4%
Difficulty:
Easy
[地址](https://leetcode.com/problems/roman-to-integer/)

解决语言：

python3

## Description

Roman numerals are represented by seven different symbols: I, V, X, L, C, D and M.

	Symbol       Value
	I             1
	V             5
	X             10
	L             50
	C             100
	D             500
	M             1000
	
For example, two is written as II in Roman numeral, just two one's added together. Twelve is written as, XII, which is simply X + II. The number twenty seven is written as XXVII, which is XX + V + II.

Roman numerals are usually written largest to smallest from left to right. However, the numeral for four is not IIII. Instead, the number four is written as IV. Because the one is before the five we subtract it making four. The same principle applies to the number nine, which is written as IX. There are six instances where subtraction is used:

I can be placed before V (5) and X (10) to make 4 and 9. 
X can be placed before L (50) and C (100) to make 40 and 90. 
C can be placed before D (500) and M (1000) to make 400 and 900.
Given a roman numeral, convert it to an integer. Input is guaranteed to be within the range from 1 to 3999.

Example 1:

	Input: "III"
	Output: 3
	
Example 2:

	Input: "IV"
	Output: 4
	
Example 3:

	Input: "IX"
	Output: 9
	
Example 4:

	Input: "LVIII"
	Output: 58
	Explanation: L = 50, V= 5, III = 3.
	
Example 5:

	Input: "MCMXCIV"
	Output: 1994
	Explanation: M = 1000, CM = 900, XC = 90 and IV = 4.

## code

```python
class Solution:
    def romanToInt(self, s: 'str') -> 'int':
        number = [900,400,90,40,9,4,1000,500,100,50,10,5,1]
        number_str = ['CM','CD','XC','XL','IX','IV','M','D','C','L','X','V','I']
        i = len(s) - 1
        j = 0
        sum = 0
        while(j <= i):
            str1 = s[j:j+1]
            str2 = s[j:j+2]
            if(str2 in number_str):
                str_index = number_str.index(str2)
                sum += number[str_index]
                j += 2
            else:
                sum += number[number_str.index(str1)]
                j += 1
        return sum		
```

## Result

Runtime: 128 ms, faster than 78.13% of Python3 online submissions for Roman to Integer.
Memory Usage: 12.5 MB, less than 100.00% of Python3 online submissions for Roman to Integer.