---
title: 有序数组合并
date: 2018-07-10 16:40:01
categories:
- practice
tags:
- practice
- algorithm
---
题目描述：
把两个有序数组合并成一个数组，第一个数组的空间正好可以容纳两个数组的元素。
<!-- more -->
## 分析
如下图所示：
{% img /images/practice/9_0.png %}
从后向前覆盖可以防止丢失数据。
## 代码
{% codeblock %}

arr1 = [None for k in range(0,6)]
arr1[0] = 1
arr1[1] = 3
arr1[2] = 5
arr2 = [2,4,6]
def merge(arr1,arr2):
    l = len(arr2) - 1
    k = len(arr1) - 1
    i = l
    j = l
    while(i >= 0 and j >= 0):
        if(arr1[i] < arr2[j]):
            arr1[k] = arr2[j]
            k -= 1
            j -= 1
        else:
            arr1[k] = arr1[i]
            k -= 1
            i -= 1
    if(j >= 0):
        while(j >= 0):
            arr1[k] = arr2[j]
            k -= 1
            j -= 1
merge(arr1,arr2)
print(arr1)

{% endcodeblock %}


