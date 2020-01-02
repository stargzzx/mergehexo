---
title: 重复值判断
date: 2018-07-10 16:29:26
categories:
- practice
tags:
- practice
- algorithm
---
题目描述：
判断数组中是否有重复值，要求空间复杂度为 O(1)。
 <!-- more -->
## 分析
如果没有空间复杂度这个条件，最好的办法是哈希表。
但现在最好的是先排序，再判断。
所以，接下来考察的是，在空间复杂度为 O(1) 的情况下，最快的排序。
O(1)
插入排序、选择排序、冒泡排序、堆排序、希尔排序
所以最好的是非递归版的堆排序。
## 代码
{% codeblock %}

def LEFT(i):
    return 2 * i + 1


def RIGHT(i):
    return 2 * i + 2


def adjustHeap(arr, length, i):
    largest = i
    while (True):
        left, right = LEFT(i), RIGHT(i)
        if (left < length) and (arr[left] > arr[i]):
            largest = left
        else:
            largest = i
        if (right < length) and (arr[right] > arr[largest]):
            largest = right
        if (largest != i):
            arr[i], arr[largest] = arr[largest], arr[i]
            i = largest
            continue
        else:
            break


def buildHeap(arr):
    length = len(arr)
    for x in range((int)((length - 1) / 2), -1, -1):
        adjustHeap(arr, length, x)


def heapSort(arr):
    buildHeap(arr)
    i = len(arr)
    while (i > 0):
        arr[0], arr[i - 1] = arr[i - 1], arr[0]
        i -= 1
        adjustHeap(arr, i, 0)

def isOne(arr):
    for i in range(1,len(arr)):
        if(arr[i] == arr[i - 1]):
            print("有重复")
            break
arr = [4, 2, 3, 7, 1, 5, 6, 4]
heapSort(arr)
isOne(arr)

{% endcodeblock %}