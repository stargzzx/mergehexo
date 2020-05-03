---
title: 深度优先(DFS)
date: 2018-07-23 21:37:17
categories:
- 算法
- 经典
tags:
- 算法
---
深度优先是一种基础算法。它分为递归版和非递归版。

关于递归的讲解，你可以看我之前的文章。

[]()

<!-- more -->

程序界有这样一种说法，那就是递归程序都可以用非递归程序替代，所用的核心就是栈。

在讲解栈之前，我想先介绍一下回溯法。

## 回溯

回溯法（探索与回溯法）是一种选优搜索法，按选优条件向前搜索，以达到目标。但当探索到某一步时，发现原先选择并不优或达不到目标，就退回一步重新选择，这种走不通就退回再走的技术为回溯法，而满足回溯条件的某一状态的点成为“回溯点”。

## 深度优先原理

原理不再表述。直接上代码。

### 递归版本
```python
class Node:
	def __init__(self,k):
		self.k = k
		self.l = None
		self.r = None

class Tree:
	def __init__(self):
		self.root = None

	def add(self,key):		#添加节点
		node = Node(key)
		if self.root is None:
			self.root = node
		else:
			q = [self.root]
			
			while True:
				p = q.pop(0)

				if p.l is None
					p.l = node
					return
				elif p.r is None:
					p.r = node
					return
				else:
					q.append(p.l)
					q.append(p.r)
# 以下是重点
def deep_Tree(node):
	if node is not None:
		print(node.k)
		if node.l is not None:
			deep_Tree(node.l)
		if node.r is not None:
			deep_Tree(node.r)
```

### 非递归版本

```python
def deep(tree):
	data = []
	data_top = -1
	way = []
	way_top = -1
	if tree is None:
		return None
	node = tree.root
	while(True):
		data.append(node)
		if node.r is not None:
			node = node.r
		elif node.r is not None:
			node = node.r
		else:
			way.append(node)
			top += 1
			node = data.pop()
```
