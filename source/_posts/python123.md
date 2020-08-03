---
title: python | 内置函数
date: 2020-08-03 10:42:56
categories:
- python
- 基础
tags:
- python
---
这里总结一下 python 的内置函数。

- [参考资料](https://www.runoob.com/python/python-built-in-functions.html)

<!-- more -->

## abs()

返回数字的绝对值。

## all()

`all()` 函数用于判断给定的可迭代参数 `iterable` 中的所有元素是否都为 `TRUE`，如果是返回 `True`，否则返回 `False`。

元素除了是 `0、空、None、False` 外都算 True。

函数等价于：

```python
def all(iterable):
    for element in iterable:
        if not element:
            return False
    return True
```

Python `2.5` 以上版本可用。

```python
>>> all(['a', 'b', 'c', 'd'])  # 列表list，元素都不为空或0
True
>>> all(['a', 'b', '', 'd'])   # 列表list，存在一个为空的元素
False
>>> all([0, 1，2, 3])          # 列表list，存在一个为0的元素
False
   
>>> all(('a', 'b', 'c', 'd'))  # 元组tuple，元素都不为空或0
True
>>> all(('a', 'b', '', 'd'))   # 元组tuple，存在一个为空的元素
False
>>> all((0, 1, 2, 3))          # 元组tuple，存在一个为0的元素
False
   
>>> all([])             # 空列表
True
>>> all(())             # 空元组
True
```

## any()

`any()` 函数用于判断给定的可迭代参数 `iterable` 是否全部为 `False`，则返回 `False`，如果有一个为 `True`，则返回 `True`。

元素除了是` 0、空、FALSE` 外都算 TRUE。

函数等价于：

```python
def any(iterable):
    for element in iterable:
        if element:
            return True
    return False
```

`Python 2.5` 以上版本可用。

```python
>>>any(['a', 'b', 'c', 'd'])  # 列表list，元素都不为空或0
True
 
>>> any(['a', 'b', '', 'd'])   # 列表list，存在一个为空的元素
True
 
>>> any([0, '', False])        # 列表list,元素全为0,'',false
False
 
>>> any(('a', 'b', 'c', 'd'))  # 元组tuple，元素都不为空或0
True
 
>>> any(('a', 'b', '', 'd'))   # 元组tuple，存在一个为空的元素
True
 
>>> any((0, '', False))        # 元组tuple，元素全为0,'',false
False
  
>>> any([]) # 空列表
False
 
>>> any(()) # 空元组
False
```

## bin()

返回一个整数 int 或者长整数 long int 的二进制表示。

## bool()

用于将给定参数转换为布尔类型，如果没有参数，返回 False。

