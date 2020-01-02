---
title: python 全局变量
date: 2018-06-11 21:12:27
categories:
- python
tags:
- python
- tutorial
---
这里集合了一些容易理解错的问题。
<!-- more -->
## 单值全局变量
### 改变单值全局变量
{% codeblock %}
a = 10
def test():
    global a
    a = 1

test()
print(a)
	# 1
{% endcodeblock %}
## 数组型复合全局变量
### del 函数改变
{% codeblock %}
data = [1,2,3]
def test():
    del(data[0])

test()
print(data)
	# [2, 3]
{% endcodeblock %}
### pop 函数改变
{% codeblock %}
data = [1,2,3]
def test():
    data.pop(0)

test()
print(data)
	# [2, 3]
{% endcodeblock %}
### 在数组内部改变
{% codeblock %}
data = [1,2,3]
def test():
    data[data.pop(0)] = 4

test()
print(data)
	# [2, 4]
{% endcodeblock %}
