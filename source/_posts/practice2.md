---
title: 排序算法总结
date: 2018-06-30 21:50:52
categories:
- practice
- 算法讲解
tags:
- sort
- practice
- algorithm
---
如题所示，另外代码是用 python 实现，附上自己的理解。
<!-- more -->

## 冒泡排序

### 原理

这个算法的名字由来是因为越大的元素会经由交换慢慢“浮”到数列的顶端，故名“冒泡排序”。

![](/images/practice/2_0.gif)

### 代码
```python
def bubbleSort(arr):		#冒泡排序
	l = len(arr)
	for i in range(l):
		for j in range(l - i):
			if j + 1 < l - i and arr[j] > arr[j + 1]:
				tmp = arr[j]
				arr[j] = arr[j + 1]
				arr[j + 1] = tmp
	return arr
```

## 选择排序

### 原理

首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

![](/images/practice/2_1.gif)

选择排序有插入版和交换版。

其中插入版是稳定的，交换版是不稳定的，下面的代码是插入版的。

### 代码

```python
def selectionSort(arr): # 选择排序
	l = len(arr)
	index = 0
	for i in range(l):
		value = arr[i]
		for j in range(i+1,l):
			if value < arr[j]:
				value = arr[j]
				index = j
		arr[index] = arr[i]
		arr[i] = value
	return arr
```

## 插入排序

### 原理

它的工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

![](/images/practice/2_2.gif)

### 自己写的代码

```python
def change(arr,i,j):
	tmp = arr[j]
	for k in range(i,j)[::-1]:
		print(k)
		arr[j] = arr[k]
	arr[i] = tmp
		
		
def insertSort(arr):	# 插入排序
	min_index =0
	l = len(arr)
	for i in 	range(l):
		for j in range(i):
			if arr[j] <= arr[i] and arr[i] < arr[j + 1] and j + 1 < l:
				print(j)
				change(arr,i,j)
	return arr	
```

### 网上流传的代码

```python
def insert_Sort(arr):
	for x in range(1,len(arr)):
		for i in range(x-1,-1,-1):
			if(arr[i] > arr[i + 1]):
				arr[i],arr[i+1] = arr[i+1],arr[i]
```

### 后面相当于变相的冒泡排序

## 归并排序

### 原理

将两个已经排序的序列合并成一个序列的操作。运用分而治之的思想。

![](/images/practice/2_3.gif)

![](/images/practice/2_4.gif)

### 代码

```python
def mergeSort(arr,first,mid,last,tmp):	# 归并排序
	i,j,k = first,mid+1,0
	while(i <= mid) and (j <= last):
		if(arr[i] <= arr[j]):
			tmp[k] = arr[i]
			i += 1
			k += 1
		else:
			tmp[k] = arr[j]
			j += 1
			k += 1
	while(i <= mid):
		tmp[k] = arr[i]
		i += 1
		k += 1
	while(j <= last):
		tmp[k] = arr[j]
		j += 1
		k += 1
	for x in range(0,k):
		arr[first + x] = tmp[x]
		
def merge_sort(arr,first,last,tmp):
	if first < last:
				mid = (int)((first + last) / 2)
				merge_sort(arr,first,mid,tmp)
				merge_sort(arr,mid+1,last,tmp)
				mergeSort(arr,first,mid,last,tmp)
def merge_array(arr):
	tmp = len(arr) * [0]
	merge_sort(arr,0,len(arr)-1,tmp)
```

## 快速排序

### 原理

通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

![](/images/practice/2_5.gif)

### 代码

```python
def quickSort(L,start,end):
	if start < end:
		i,j,pivot = start,end,L[start]
		while(i < j):
			while(i < j) and (L[j] >= pivot):
				j -= 1
			if(i < j):
				L[i] = L[j]
				i += 1
			while(i < j) and (L[i] < pivot):
				i += 1
			if(i < j):
				L[j] = L[i]
				j -= 1
		L[i]=pivot
		quickSort(L,start,i-1)
		quickSort(L,i+1,end)
```

## 堆排序

### 原理

堆分为大根堆和小根堆，是完全二叉树。

![](/images/practice/2_6.gif)

### 过程图解

数据如下：

	arr = [4,2,3,7,1,5,6]

### 构建大顶堆

![](/images/practice/2_7.png)

### 输出以及调整大顶堆

![](/images/practice/2_8.png)

代码最后的答案可能和图片有点出入，是因为图片我才用比较右在比较左，而代码正好相反。小事情，在这里提一下。

### 代码

```python
def LEFT(i):
	return 2 * i + 1
def RIGHT(i):
	return 2 * i + 2
def adjustHeap(arr,length,i):
	largest = i
	while(True):
		left,right = LEFT(i),RIGHT(i)
		if(left < length) and (arr[left] > arr[i]):
			largest = left
		else:
			largest = i
		if(right < length) and (arr[right] > arr[largest]):
			largest = right
		if(largest != i):
			arr[i],arr[largest] = arr[largest],arr[i]
			i = largest
			continue
		else:
			break
			
def buildHeap(arr):
	length = len(arr)
	for x in range((int)((length - 1)/2),-1,-1):
		adjustHeap(arr,length,x)

def heapSort(arr):
	buildHeap(arr)
	i = len(arr)
	while(i > 0):
		arr[0],arr[i-1] = arr[i-1],arr[0]
		i -= 1
		adjustHeap(arr,i,0)

arr = [4,2,3,7,1,5,6]
```

## 时间复杂度

	O(n * log(n))
	快速排序、希尔排序、堆排序、归并排序
	当然快速排序在最坏的情况下是 O(n ^ 2)
	O(n ^ 2)
	大部分的算法都是这个
	O(d(n + e))
	基数排序
	
## 空间复杂度

	O(1)
	插入排序、选择排序、冒泡排序、堆排序、希尔排序
	O(logN)
	快速排序、堆排序（使用递归）
	O(N)
	归并排序
	O(M) # M 代表桶数量
	计数排序、计数排序

## 稳定性

	稳定的排序
	冒泡排序、插入排序、选择排序（插入版）、归并排序、计数排序、基数排序、桶排序
	不稳定的排序
	选择排序（交换版）、快速排序、希尔排序、堆排序

## 补充1

	没有什么算法是绝对的好坏。
	选择哪种算法还是要看具体的数据。
	比如人的身高排序，因为数据范围很小，所以计数排序就很占优势。

## 补充2

	快速排序之所以叫快速排序，并不是它的性能最好。
	而是，在最好的情况下，它的常量系数比较低。
	
## 补充3

	工程上的排序是综合排序，并不是单独的排序。






