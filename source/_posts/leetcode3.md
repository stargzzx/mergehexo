---
title: Offer 50. 第一个只出现一次的字符
date: 2020-07-28 11:09:32
categories:
- 算法
- leetcode
- 简单
tags:
- leetcode
---
记录每一次自己刷题，今日完成任务 1/3 。

<!-- more -->

- python3

<br/>

# 题目简介

<br/>

- [Offer 50. 第一个只出现一次的字符](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

示例:

	s = "abaccdeff"
	返回 "b"

	s = "" 
	返回 " "


限制：

- 0 <= s 的长度 <= 50000

<br/>

# 我的解法

<br/>

## 解法一 「有序哈希」

- 执行用时：132 ms 击败了 39.78% 的用户
- 内存消耗：13.8 MB 击败了 100.00% 的用户

```python
class Solution:
    def firstUniqChar(self, s: str) -> str:
        if not len(s):
            return ' '
        L = {}
        for r in s:
            is_exit = L.get(r,None)
            if not is_exit:
                L[r] = 1
            else:
                L[r] = is_exit + 1
        for key,value in L.items():
            if value == 1:
                return key
        return " "
```

这个主要是利用了 python 的字典，但是，并不是每一个版本的 python 这么写都能得到正确的答案，python 的版本必须最低是 python3.6 。

关于这个问题，你可以参考我下面的博文。

- [python | 字典总结](https://benpaodewoniu.github.io/2020/06/24/python119/)

## 优化解法一

```python
class Solution:
    def firstUniqChar(self, s: str) -> str:
        dic = {}
        for c in s:
            dic[c] = not c in dic
        for k, v in dic.items():
            if v: return k
        return ' '
```


