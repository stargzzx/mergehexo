---
title: leetcode6
date: 2020-07-29 14:20:39
categories:
- 算法
- leetcode
- 写前须知
tags:
- leetcode
---
这里面有一些是 leetcode 内在的一些坑，在此记录，祝你更愉快的刷题！

<!-- more -->

## 类变量和全局变量

编写如下代码

```python
class Solution:

	d = {}

	def update_map():
		if (...):
			self.d[...] = ...
```

如果有类变量和全局变量，上述提交的代码执行可能会失败，因为， leetcode 是这样的执行的。

	s = Solution()
	s.update()

其只会初始化一次实例，然后，利用这个实例来重复的调用函数，导致，下一个问题，可能存着上一个问题遗留下的结果，所以，要每一个问题之后，相关的全局、类变量都要清空。

```python
class Solution:

	d = {}

	def update_map():
		d.clear()
		if (...):
			self.d[...] = ...
```
