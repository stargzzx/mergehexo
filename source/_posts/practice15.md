---
title: 词语变形
date: 2018-07-14 17:02:49
categories:
- practice
- 算法练习
tags:
- practice
- algorithm
---
问题描述：

所谓的词语变形就是给定两个数据，要求这两个数据中每一个元素数据出现的次数相同，而不关心两个数据本身。

123     321 是互为变形数

123     1231 不互为变形数

<!-- more -->

## 分析

这个问题的最优解就是利用哈希表。

如果只是包含普通字符，即 0 ~ 255，就建立长度为 266 的数组。

如果包含汉字或者其他，就建立长度为 65536 的数组。

最后直接比较哈希表即可。 

## 代码

当然代码有一点粗糙，毛毛雨啦，只需要知道思想就好啦。

```python
arr1 = [0 for k in range(256)]
arr2 = [0 for k in range(256)]
a = "11543123"
b = "54112131"
for i in range(len(a)):
    arr1[ord(a[i])] += 1

for j in range(len(b)):
    arr2[ord(b[j])] += 1

for i in range(256):
    if(arr1[i] != arr2[i]):
        print("no")
        break
    if(i == 255):
        print("yes")
        break
    i += 1
```
