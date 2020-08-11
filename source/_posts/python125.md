---
title: python | 值比较和引用比较
date: 2020-08-11 14:49:25
categories:
- python
- 基础
tags:
- python
---
这里主要讲述了 python 中 `==` 和 `is` 的异同。

<!-- more -->

<br/>

# 参考资料

<br/>

- [Python中的引用比较、值比较与对象池](https://blog.csdn.net/weixin_35653315/article/details/71640473)
- [Python：对象的值比较 == 和引用判别 is](https://blog.csdn.net/m0_46527503/article/details/105337394)

<br/>

# 解析

<br/>

推荐你看我之前写的博文。

- [python | 赋值、引用、拷贝、作用域 深入理解](https://benpaodewoniu.github.io/2020/05/23/python111/)

- `==` 运算符判断 两个变量指向的对象的值是否相同
- `is` 运算符判断 两个变量是否指向同一对象，同一个内存地址

## 不可变对象

- 基本类型
- 字符串
- 元组

```python
a = 1000
b = 1000
print(a is b) # True
print(a == b) # True

a = '哈哈'
b = '哈哈'
print(a is b) # True
print(a == b) # True

a = (1, 2, 3)
b = (1, 2, 3)
print(a is b) # True
print(a == b) # True
```

## 可变对象

- 字典
- 列表
- 集合
- 自定义对象

```python
a = {'h': 1}
b = {'h': 1}
print(a is b) # False
print(a == b) # True

a = {1, 2, 3}
b = {1, 2, 3}
print(a is b) # False
print(a == b) # True


# 自定义对象
class Node:
    def __init__(self, x):
        self.val = x
        self.next = None


n1 = Node(1)
n2 = Node(1)
print(a is b) # False
print(a == b) # True
```