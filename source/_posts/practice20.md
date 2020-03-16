---
title: 空格替换
date: 2018-07-16 09:10:41
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：

给定一个字符串 str，将其中所有的空格字符替换成“%20”[三个字符]。假设 str 后面有足够的空间。

<!-- more -->

## 分析

假设有如下数据

	a b c一共是三个字符，两个空格
	所以共需要空间为 5 + 2 * 2[ 5 是原来的字符串长度，2 * 2 表示额外需要 4 个空间]
	数组变成：
	a b c    ，用 i 标记数组的最后的位置，即 index 为 8，j 标记原来数组位置，即 c 的位置，index 为 4
	当 i 指向非空格字符时，j 的位置复制这个字符，j - 1，i - 1
	当 i 指向空格字符时，j 的位置变成 %20 ，j - 3，i - 1

## 代码

```python
def change(str):
    arr = str.split(" ")
    i = len(str) - 1
    print(i)
    j = len(str) + str.count(" ") * 2 - 1
    new = [0 for k in range(j + 1)]
    while(i > -1 and j > -1):
        if(str[i] != " "):
            new[j] = str[i]
            i -= 1
            j -= 1
        else:
            i -= 1
            new[j] = "0"
            new[j - 1] = "2"
            new[j - 2] = "%"
            j -= 3
    print("".join(new))


arr = " a b c "
change(arr)
```
