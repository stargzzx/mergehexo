---
title: 拓扑结构相同子树
date: 2018-07-12 11:19:46
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：

{% img /images/practice/13_0.JPG %}

<!-- more -->

这个问题给出两种解法，一般解和最优解。

## 一般解

### 分析

首先将二叉树序列化，然后暴力拟合。

时间复杂度时 O(N * M)[N 是 TXT 的长度 M 是 STR 的长度]

### 代码

略

## 最优解

### 分析

序列化，然后用 KMP 拟合。

[二叉树创建、四大遍历](https://benpaodewoniu.github.io/2018/06/30/practice3/)

[KMP详解](https://benpaodewoniu.github.io/2018/07/12/practice14/)

这样最后的时间复杂度是 O(N + M)[N 是 TXT 的长度 M 是 STR 的长度]

空间复杂度是 O(M)

当然下面代码中的序列化要改变，因为添加了 ！ ，KMP 的返回值也要改变，但是我觉得麻烦，就没改。因为懒。

### 代码

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

class Node:  # 节点类
    def __init__(self, k):
        self.k = k
        self.l = None
        self.r = None


class Tree:  # 二叉树类
    def __init__(self):
        self.root = None
        self.arr = []

    def add(self, key):  # 添加节点 借助数组构建节点
        node = Node(key)
        if self.root is None:
            self.root = node
        else:
            q = [self.root]

            while True:
                p = q.pop(0)

                if p.l is None:
                    p.l = node
                    return
                elif p.r is None:
                    p.r = node
                    return
                else:
                    q.append(p.l)
                    q.append(p.r)

    def preorder(self,node):	# 前序遍历
        if node is None:
            return
        else:
            self.arr.append("%s!" % node.k)
            self.preorder(node.l)
            self.preorder(node.r)

tree1 = Tree()
tree1.add(1)
tree1.add(3)
tree1.add(5)
tree1.add(5)
tree1.add(7)
tree1.add(3)
tree1.add(2)
tree1.add(9)
tree1.add(10)
tree1.preorder(tree1.root)
str = "".join(tree1.arr)
tree2 = Tree()
tree2.add(3)
tree2.add(5)
tree2.add(7)
tree2.add(9)
tree2.add(10)
tree2.preorder(tree2.root)
substr = "".join(tree2.arr)
print(str)
print(substr)
next = [None for k in range(len(substr))]
getNext(substr,next)
print(KMP(str,substr,next))
{% endcodeblock %}
