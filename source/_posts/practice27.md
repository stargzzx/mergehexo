---
title: 可查询最值栈
date: 2018-07-23 22:39:17
categories:
- practice
tags:
- practice
- algorithm
---
题目描述：

实现一个特殊的栈，在实现栈的基本功能的基础上，再实现返回栈中最小元素的操作 getmin 。

要求：

1. pop 、 push 、 getMin 操作的时间复杂度都是 O(1)

2. 设计的栈类型可以使用现成的栈结构。

<!-- more -->

## 原理

只说明 getMIN 的原理。

算法需要借助两个栈，第一个栈是传统栈，第二个栈是辅助栈。辅助栈的功能是第一步将最初的栈元素压入栈顶，如果传统栈压入的元素小于辅助栈的栈顶，则在辅助栈中压入新元素，如果大于辅助栈的栈顶，则压入原辅助栈的栈顶。

过程如图：

{% img /images/practice/27.png %}

## 代码

下面代码只是实现了 getMin 的辅助数据，其他的方法就不实现了。

{% codeblock %}
data = [5,4,1,2]
stack1 = [None for k in range(len(data))]
stack2 = [None for k in range(len(data))]
element = None
for i in range(len(data)):
    if i is 0:
        element = data.pop(0)
        stack1[i] = element
        stack2[i] = element
    else:
        element = data.pop(0)
        if element > stack2[i - 1]:
            stack1[i] = element
            stack2[i] = stack2[i - 1]
        else:
            stack1[i] = element
            stack2[i] = element

print(stack1)
print(stack2)
{% endcodeblock %}
