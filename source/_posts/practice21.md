---
title: 合法括号序列判断
date: 2018-07-16 09:38:21
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：
给定一个字符串 str ，判断是不是整体有效的括号字符串。
str = "()" return True
str = ")(" return False
这个问题要求时间复杂度为 O(N),空间复杂度为 O(1)
<!-- more -->
## 分析
其实，这道题是有缺陷的。
这道题要想做到上面的时间复杂度要求，是需要对 str 有约束的。
约束就是 str 中的括号必须是缺少，而不是乱序，比如 str = ")(" 是检测不出的。
至于为什么检测不出，请看原理。
### 原理
我们用一个 number 记录括号出现的次数。
出现 "(" 的时候，num += 1
出现 ")" 的时候，num -= 1
当最后， num ！= 0 的时候，证明出错
## 代码
{% codeblock %}

def can(arr):
    num = 0
    for i in range(len(arr)):
        if arr[i] == "(":
            num += 1
        elif arr[i] == ")":
            num -= 1
    if(num == 0):
        print("yes")
    else:
        print("no")

arr = "(()))"
can(arr)

{% endcodeblock %}
