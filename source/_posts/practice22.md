---
title: 最长无重复字符子串
date: 2018-07-16 10:19:42
categories:
- practice
- 算法练习
tags:
- practice
- algorithm
---
问题描述：

给定一个字符串 str，返回 str 的最长无重复字符子串的长度。

如：

str = "abcd" 返回 4

str = "abcb"  因为最长无重复字符子串为 "abc" ，返回 3

要求时间复杂度为 O(N),空间复杂度为 O(N)

<!-- more -->

## 分析

这里一共给出两种解法，一个一般解，一个最优解

### 一般解

我们一提到最长，最短这种和重复性相关的问题，就要想到利用 hash。

所以，如果我们只是用 hash 记录的话，我们的时间复杂度是 O(N ^ 2)[要借助两层循环]，空间复杂度是 O(256) + 1 是根据是否有汉字来区别的，那个 + 1 是因为要记录最长的长度。

hash 表只记录字符是否出现，所以一共只有两个值，一个为 0(没出现)，一个为 1 (出现了)。每循环一次都要将 hash 重新初始化为 0。

所以会出现以下两种情况：

	当查阅内循环的某一个值的 hash 表中的值为 1 的时候。
		说明有两种情况，一个是内循环的这个值和外循环的这个值一样，比如 abca
						一个是在这个字符串中有相同的字符，比如 abcc
		无论是哪种情况，我们都要和 lenl 比较，以确定最大的 lenl，并跳出循环
		
	当内循环结束的时候，因为没有重复字符，所以相关 hash 中的位置都是 1 ，我们依然要比较lenl 和结尾 - 开头的长度

代码非常优秀，即便是一般解，因为运用的思想需要值得学习，如果按照我的思想，我会大量使用 if 语句，来判断。我的最开始的样例就不写了(因为懒，更重要的是忘了QAQ)。

代码如下：

```python
def hash(arr):
    l = len(arr)
    lenl = 1
    for i in range(l):
        hash = [0 for k in range(256)]
        for j in range(i,l):
            if(hash[ord(arr[j])] == 0):
                hash[ord(arr[j])] = 1
            else:
                if( j - i > lenl):
                    lenl = j - i
                break
            if(j == l - 1):
                if( j - i > lenl):
                    lenl = j - i + 1
    return lenl

arr = "abcaacdsxaababxhsgdn"
print(hash(arr))
```

### 最优解

动态规划 + hash

动态规划算法的思想，将问题分解为子问题的解，找到重叠子问题和最优子结构，对需要重复计算的结果进行存储。

我对上面的解释也不是很理解啊，但是不写又觉得缺少点什么。

我们的最优解的时间复杂度为 O(N)，空间复杂度为 O(256) + 1 + 1,其中第一个 + 1 是记录子串[不一定是最长，只需要保证这个子串中没有重复元素即可]的开头位置，第二个 + 1 是记录最长子串的长度。

算法的过程如下：

代码还是出错，抽时间再看。

```python
def maxSubString(arr):
	front = 0
	lenl = 0
	hash = [-1 for k in range(256)]
	for i in range(len(arr)):
		if(hash[ord(arr[i])] != -1):
			if(hash[ord(arr[i])] > front):
				front = hash[ord(arr[i])] + 1
				if(i - hash[ord(arr[i])] > lenl):
					print("li")
					lenl = i - hash[ord(arr[i])]
			elif(hash[ord(arr[i])] < front):
				lenl += 1
			else:
				front = hash[ord(arr[i])] + 1
		else:
			lenl += 1
		hash[ord(arr[i])] = i
	print(front)
	return lenl

arr = "abcasfafdecfgxc"
print(maxSubString(arr))
```

