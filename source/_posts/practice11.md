---
title: 有序矩阵查找
date: 2018-07-10 20:28:39
categories:
- practice
tags:
- practice
- algorithm
---
题目描述：

在行列都排好序的矩阵中，找到需要的数，矩阵如下：
	
	1 3 5 6
	2 4 6 7
	3 5 8 8
	5 7 9 10
	
找到5就可以返回。

<!-- more -->

## 分析

我们从右上角的那一个开始分析，如果右上角那一个大于目标值，又因为是按序排列，所以那一列的值都比目标值大，所以列数应该提前。

如果所在数比目标值小，则证明这一行前面的数都比它小，则行数应该增加，直到找到目标值，或者越界。

如下图所示：

{% img /images/practice/11_0.png %}

这个算法的时间复杂度为 O(M + N)[其中 M N 为矩阵的行数和列数]，空间复杂度为 O(1)。

## 代码

{% codeblock %}
def returnIndex(arr,h,l,goal):
    i = 0
    j = l - 1
    while(True):
        if(i > h - 1 or l < 0):
            print("no number")
            return
        if(arr[i][j] > goal):
            j -= 1
        elif(arr[i][j] < goal):
            i += 1
        else:
            print("%d and %d" % (i,j))
            return



arr = [[2,4,5,6],[3,4,6,7],[4,5,7,8],[6,7,8,9]]
returnIndex(arr,4,4,15)
{% endcodeblock %}

