---
title: leetcode 5
date: 2019-02-17 10:13:10
categories:
- practice
tags:
- leetcode
- practice
- algorithm
---
这是刷的第 5 个 leetcode 题。

<!-- more -->

## 介绍

Title：
Roman to Integer    
Acceptance：
30.5%
Difficulty:
Medium
[地址](https://leetcode.com/problems/add-two-numbers/)

解决语言：

python3

## Description
>You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.
You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Example:

	Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
	Output: 7 -> 0 -> 8
	Explanation: 342 + 465 = 807.
	
## code

```python
class Solution:
    def addTwoNumbers(self, l1: 'ListNode', l2: 'ListNode') -> 'ListNode':
        number1 = 0
        number2 = 0
        i = 0
        j = 0
        while(l1 != None):
            number1 = number1 + l1.val * pow(10,i)
            l1 = l1.next
            i += 1
        while (l2 != None):
            number2 = number2 + l2.val * pow(10, j)
            l2 = l2.next
            j += 1
        number = number1 + number2
        new_node = ListNode(number % 10)
        number = number // 10
        final = new_node
        while(number):
            new = ListNode(number % 10)
            new_node.next = new
            new_node = new
            number = number // 10
        return final
			
```

## Result

Runtime: 108 ms, faster than 77.38% of Python3 online submissions for Add Two Numbers.
Memory Usage: 12.5 MB, less than 100.00% of Python3 online submissions for Add Two Numbers.