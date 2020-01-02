---
title: 字符串移位
date: 2018-07-15 16:21:15
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：
给定一个字符串 str，和一个正数 i，i代表 str 中的位置，将 str[0...i] 移到右侧，str[i + 1,N - 1] 移到左侧。
要求时间复杂度为 O(N),空间复杂度为 O(1)。
<!-- more -->
## 分析
很简单
只需要借助逆序，进行三步。
1234  i = 1
第一步：
将 0 - i 间的数进行逆序
得：2134
第二步将 i + 1，N - 1 间的数逆序
得：2143
第三步全部逆序
得：3412
## 代码
{% codeblock %}

def reverse(i,j,arr):
    arr = list(arr)
    while(i < j):
        arr[i],arr[j] = arr[j],arr[i]
        i += 1
        j -= 1
    return "".join(arr)
arr = "1234abc"
arr = reverse(0,3,arr)
arr = reverse(4,6,arr)
arr = reverse(0,len(arr) - 1,arr)
print(arr)

{% endcodeblock %}