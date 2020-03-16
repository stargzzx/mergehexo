---
title: 句子逆序
date: 2018-07-15 16:03:32
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：

给定一个句子，要求返回单词间的逆序。

如：

i am dog => dog am i

<!-- more -->

## 分析

只需要写出一个根据初始，结束位置完全转置的函数。

一共分为两步：

第一步：

将所有字符转置 abc d => d cba

第二部：

将独立的单词逆序

d cba => d abc

## 代码

```python
def reverse(i,j,arr):
    arr = list(arr)
    while(i < j):
        arr[i],arr[j] = arr[j],arr[i]
        i += 1
        j -= 1
    return "".join(arr)
arr = "123 abc a cd"
arr = reverse(0,len(arr) - 1,arr).split(" ")
new = ""
for i in range(len(arr)):
    new = new + reverse(0,len(arr[i]) - 1,arr[i]) + " "
print(new)
```
