---
title: 旋转词
date: 2018-07-14 18:16:11
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：

如果一个字符串 str，把字符串 str 前面任意的部分挪到后面去形成的字符串叫做 str 的旋转词。

比如： str = “1234” 它的旋转词有 “1234” “2341” “3412” “4123”。

给定两个字符串 a 和 b，判断是否互为旋转词。

<!-- more -->

## 分析

其实只需要构建一个新的字符串，比如 new = a + a，只需要判断 b 是否在 new 中即可。

在则互为旋转词，不在则不是。

对于如何找 b 在 new 中，只需要使用 KMP 算法即可。

[KMP详解](https://benpaodewoniu.github.io/2018/07/12/practice14/)

所以时间复杂度为 O(N)。

## 代码

{% codeblock %}
def getNext(substr,next):
    i = 0
    j = -1
    next[0] = -1
    while(i < len(substr) - 1):
        if(j == -1 or substr[i] == substr[j]):
            i += 1
            j += 1
            next[i] = j
        else:
            j = next[j]


def KMP(str,substr,next):
    i = 0
    j = 0
    while(i <= len(str) - 1 and j <= len(substr) - 1):
        if(j == 0 or str[i] == substr[j]):
            i += 1
            j += 1
        else:
            j = next[j]
    if(j > len(substr) - 1):
        return i - len(substr)
    else:
        return -1

a = "1234"
b = "3142"
new = a + a
next = [0 for k in range(len(b))]
getNext(b,next)
if(len(a) != len(b)):
    print("no")
else:
    index = KMP(new,b,next)
    if(index == -1):
        print("no")
    else:
        print("yes")
{% endcodeblock %}