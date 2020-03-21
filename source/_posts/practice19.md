---
title: 拼接最小字典序
date: 2018-07-15 16:48:06
categories:
- practice
- 算法练习
tags:
- practice
- algorithm
---
问题描述：

给定一个字符串类型的数组 strs ，请找到一种拼接顺序使得所有字符串拼接起来组成的大字符串是所有可能性中字典顺序最小的，并返回这个大字符串。

比如：

strs = ["abc","de"] => "abcde"

要求时间复杂度为 O(N * log(N))

<!-- more -->

## 分析

### 错误分析

根据每个单独的字符串的字典顺序排列，会导致下面这种情况：

	["ba","b"] => "bba"
	
我也不知道什么是字典序，就这样吧。

### 正确的方式

其实这道题本质上是一个排序问题，为了满足题目要求的时间复杂度，我们可以选择快排。

但是如何比较大小？

所以，我们要自定义一个比较函数，如下：

如果 str1 + str2 < str2 + str1，则 str1 大，否则 str2 大。

## 代码

```python
def compare(arr1,arr2):
    for i in range(len(arr1)):
        if(arr1[i] > arr2[i]):
            return 1
        elif(arr1[i] < arr2[i]):
            return -1
    return 0

def quickSort(L,start,end):
	if start < end:
		i,j,pivot = start,end,L[start]
		while(i < j):
			arr1 = L[j] + pivot
			arr2 = pivot + L[j]
			while(i < j) and (compare(arr1,arr2) >= 0):
				j -= 1
				arr1 = L[j] + pivot
				arr2 = pivot + L[j]
			if(i < j):
				L[i] = L[j]
				i += 1
				arr1 = L[i] + pivot
				arr2 = pivot + L[i]
			while(i < j) and (compare(arr1,arr2) < 0):
				i += 1
				arr1 = L[i] + pivot
				arr2 = pivot + L[i]
			if(i < j):
				L[j] = L[i]
				j -= 1
				arr1 = L[j] + pivot
				arr2 = pivot + L[j]
		L[i]=pivot
		quickSort(L,start,i-1)
		quickSort(L,i+1,end)

arr = ["b","ba","a","abc","e","ar"]
quickSort(arr,0,len(arr) - 1)
print(compare("aababc","abcaab"))
print(arr)
```




