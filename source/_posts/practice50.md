---
title: leetcode 6
date: 2019-02-19 09:40:20
categories:
- practice
tags:
- leetcode
- practice
- algorithm
---
这是刷的第 6 个 leetcode 题。

<!-- more -->

## 介绍

Title：
Longest Common Prefix
Acceptance：
32.9%
Difficulty:
Easy

[地址](https://leetcode.com/problems/longest-common-prefix/)

解决语言：

python3

## Description

>Write a function to find the longest common prefix string amongst an array of strings.
If there is no common prefix, return an empty string "".

Example 1:

	Input: ["flower","flow","flight"]
	Output: "fl"

Example 2:

	Input: ["dog","racecar","car"]
	Output: ""
	Explanation: There is no common prefix among the input strings.

## code

```python
class Solution:
    def longestCommonPrefix(self, strs: 'List[str]') -> 'str':
        i = 0
        j = 1
        f = 0
        g = 0
        number = 0
        if strs == []:
            return ""
        if len(strs) == 1:
            return strs[0]
        while(j <= len(strs[0])):
            target = strs[0][i:j]
            for k in range(1,len(strs)):
                if(target not in strs[k][i:j]):
                    break
                if(k == len(strs) - 1):
                    if(number < j - i):
                        number = j - i
                        f = i
                        g = j
            j += 1
        return strs[0][f:g]		
```

我自己写的代码太过于冗余，下面我贴一下其他人写的代码，供自己学习。

```python
class Solution(object):
    def longestCommonPrefix(self, strs):
        """
        :type strs: List[str]
        :rtype: str
        """
        cpt=""
        if( not strs):
            return cpt
        minnum=min([len(x) for x in strs])
        same=True
        for i in range(minnum):
            check=[x[i] for x in strs]
            for i in range(len(check)):
                if (check[i] != check[0]):
                    same = False
                    break
            if(not same):
                break
            if(same):
                cpt += check[0]
        return cpt	
```

当然，我在原来代码的基础上进行了改变。

这份代码的思想和我一开始想的相似，但是，他的构造也很巧妙。

先是得到字符中最短的长度，然后再进行字符串中每一个字符的比较，不错，学习了。

## 注解

之前写题目的时候没有清楚，以为是寻找最长匹配，而题目中的要求是寻找最长前缀匹配。

所以，在这里贴一下我的最长匹配代码，所以啊，磨刀不误砍柴功，干任何事都要仔细的思考之后再做，当然，三思而殆，这点也要注意。

```python
class Solution:
    def longestCommonPrefix(self, strs: 'List[str]') -> 'str':
        i = 0
        j = 1
        f = 0
        g = 0
        number = 0
        if strs == []:
            return ""
        if len(strs) == 1:
            return strs[0]
        while(j <= len(strs[0])):
            target = strs[0][i:j]
            for k in range(1,len(strs)):
                if(target not in strs[k]):
                    break
                if(k == len(strs) - 1):

                    if(number < j - i):

                        number = j - i
                        f = i
                        g = j
            if(j == len(strs[0])):
                i += 1
                j = i + 1
            j += 1
        return strs[0][f:g]		
```

## Result

Runtime: 48 ms, faster than 44.46% of Python3 online submissions for Longest Common Prefix.
Memory Usage: 12.5 MB, less than 100.00% of Python3 online submissions for Longest Common Prefix.