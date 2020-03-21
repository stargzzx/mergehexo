---
title: 双栈队列
date: 2018-07-30 18:22:43
categories:
- practice
- 算法讲解
tags:
- practice
- algorithm
---
问题描述：

编写一个类，只能用两个栈结构实现队列，支持队列的基本操作（add,poll,peek）。

<!-- more -->

## 原理

很简单，甚至有一点脱裤子放屁的感觉。

将数据全部导入栈 1 ，再讲栈 1 的数据全部倒入栈 2。

这样，栈 2 的出队列元素，就和队列的出队列元素一样了。

是不是很简单，我都不想写代码了。

## 注意点

1. 如果倾倒栈 1 的元素，必须一次性倒空。

2. 如果栈 2 中有元素，则不能倾倒栈 1 的元素。

## 代码

```python
data = [5,4,3,2,1]
stack1 = [None for k in range(len(data))]
top1 = -1
stack2 = [None for k in range(len(data))]
top2 = -1

for i in range(len(data)):
    stack1[i] = data[i]
    top1 += 1
for i in range(len(data)):
    stack2[i] = stack1[top1 - i]
    top2 += 1
print(stack2)
```
