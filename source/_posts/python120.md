---
title: python | 如何使运行效率更快
date: 2020-07-01 23:02:56
categories:
- python
- 进阶
tags:
- python
---
这里记录着我关于 python 运行速度的一些技巧。

<!-- more -->

<br/>

# 数组

<br/>

## 初始化

```python
a = [0] * 1000
```

序列初始化
```python
a = list(range(1000000))
快于
a = [x for x in range(1000000)]
```