---
title: 二分法详解
date: 2018-06-30 22:02:45
categories:
- practice
tags:
- practice
- 二分法
- algorithm
---
如题所示，另外代码是由 python 实现。

<!-- more -->

## 得到最后一个重复的位置

```python
def gainLast(arr,num):	# 得到最后一个重复的位置
	l = 0
	r = len(arr)
	if(r == 0):
		return
	if(arr[0] > num):
		return -1
	if(arr[r - 1] <= num):
		return r - 1
	index = (l + r) // 2
	while(True):
		if arr[index] > num:
			r = index - 1
			index = (l + r) // 2
		if arr[index] <= num:
			l = index + 1
			index = (l + r) // 2
		if(arr[index] <= num and arr[index + 1] > num):
			break
		if(r == l):
			index -= 1
			break
	return index
```

### 测试

```python
arr = [1,2,2,2,3,5,6,6,7,9]
print(gainLast(arr,0))
	# -1
print(gainLast(arr,1))
	# 0
print(gainLast(arr,2))
	# 3
print(gainLast(arr,9))
	# 9
print(gainLast(arr,10))
	# 9
```

## 得到第一个重复的位置

```python
def gainFirst(arr,num):	# 得到第一个重复的位置
	l = 0
	r = len(arr)
	if(r == 0):
		return
	if(arr[0] >= num):
		return -1
	if(arr[r - 1] < num):
		return r - 1
	index = (l + r) // 2
	while(True):
		if arr[index] >= num:
			r = index - 1
			index = (l + r) // 2
			if(index < 0):
				return -1
		if arr[index] < num:
			l = index + 1
			index = (l + r) // 2
		if(arr[index] < num and arr[index + 1] >= num):
			break
		if(r == l):
			index -= 1
			break
	return index
```

### 测试

```python
arr = [1,2,2,2,3,5,6,6,7,9]
print(gainLast(arr,0))
	# -1
print(gainLast(arr,1))
	# -1
print(gainLast(arr,2))
	# 0
print(gainLast(arr,9))
	# 8
print(gainLast(arr,10))
	# 9
```
