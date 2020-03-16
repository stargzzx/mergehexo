---
title: 小范围排序
date: 2018-07-03 11:26:58
categories:
- practice
tags:
- practice
- algorithm
---
题目描述：

已知一个几乎有序的数组，几乎有序是指，如果把数组排好顺序的话，每个元素移动距离不超过 K ，并且 K 相对于数组长度来说很小，请问用什么方法对其排序好。

 <!-- more -->

## 分析

时间复杂度为 O(N) 的排序算法

计数排序、基数排序

这两种算法不基于比较，所以不适合所有情况，舍弃

时间复杂度为 O(N ^ 2) 的排序算法

冒泡排序、选择排序

这两个算法与原始数组序列无关，最终结果都是 O(N ^ 2)

插入排序

因为插入排序和原始顺序有关，插入移动不会超过 K ，在这个案例中有很好的表现，最终的结果是O(N * K)

时间复杂度为 O(N * logN) 的排序算法

快速排序与原始序列无关

归并排序与原始序列无关

最终的答案是改进后的堆排序。

因为每次移动不会超过 K 次，那么最小值一定在 0 - K-1 之中，用 0 - K - 1建立小顶堆，取出最小值后，在用 1 - K 建立第二个最小堆，以此类推，直到全部排序完成。

时间复杂度是 O(N * log(K))

## 代码

```python
def LEFT(x):
    return 2 * x + 1

def RIGHT(x):
    return 2 * x + 2

def heapSort(arr,length):
    l = (int)((length - 1) / 2)
    while(l >= 0):
        tmp = l
        left = LEFT(l)
        right = RIGHT(l)
        if(left < length and left <= (len(arr) - 1)):
            if(arr[left] < arr[l]):
                tmp = left
        if(right < length and right <= (len(arr) - 1)):
            if(arr[right] < arr[tmp]):
                tmp = right
        if(tmp != l):
            arr[tmp],arr[l] = arr[l],arr[tmp]
        if(l == 0 ):
            if(len(arr) != 0):
                print(arr.pop(0))
            l = (int)((length - 1) / 2)
            continue
        l -= 1
        if(len(arr) == 0):
            break


arr = [2,1,4,3,6,8,5,7,9]
heapSort(arr,3)
```

## 写代码途中所遇错误

最开始我打算用切片的方式，把数组进行长度为三的切片，然后传递给函数，结果发现，切片后传递的只是形参，具体如下代码。

```python
def test(arr):
    arr[0] = 10

arr = [1,1,1,1]
test(arr[0:2])
print(arr)
	# [1, 1, 1, 1]
```

