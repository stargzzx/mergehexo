---
title: 二叉树创建、四大遍历
date: 2018-06-30 21:57:54
categories:
- 算法
- 经典
tags:
- 算法
---
如题所示，另外代码是由 python 实现。

<!-- more -->

## 构建

过程如图：

```python
class Node: #节点类
	def __init__(self,k):
		self.k = k
		self.l = None
		self.r = None

class Tree: #二叉树类
	def __init__(self):
		self.root = None

	def add(self,key):		#添加节点 借助数组构建节点
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
```

## 遍历说明

二叉树的遍历其实是二叉树的序列化。

如下面代码所示，如果节点为空，则用 #！ 代替

如果非空，则用 %s！ 代替 （%s 代表那个 value）

在这里你可能有一个疑问，为什么最后总要有一个 ！ 作为分解？

假设没有 ！ 为分界，那 23 是 2 和 3 还是 23 ？

所以用 ！ 作为分界，可以很好的规避上述问题。

## 层次遍历

```python
	def levelOrder(self):  # 按层次遍历
		l = []             # 借助队列
		last = None
		nlast = None
		l.append(self.root)
		while(len(l) != 0):
			node = l.pop(0)
			print(node.k,end = "")
			if not (node.l is None):
				nlast = node.l
				l.append(node.l)
			if not(node.r is None):
				nlast = node.r
				l.append(node.r)
			last = nlast
			if last == node:
					print('\n')
```

## 前序遍历

```python
	def preorder(self,node):	# 前序遍历
		if node is None:
			print("#!",end = "")
			return
		else:
			print("%s!" % node.k,end = "")
			self.preorder(node.l)
			self.preorder(node.r)
```

## 中序遍历

```python
	def midorder(self,node): # 中序遍历
		if node is None:
			print("#!",end = "")
			return
		else:
			self.midorder(node.l)
			print("%s!" % node.k,end = "")
			self.midorder(node.r)
```

## 后序遍历

```python
	def postorder(self,node):	#后序遍历
		if node is None:
			print("#!",end = "")
			return
		else:
			self.postorder(node.l)
			self.postorder(node.r)
			print("%s!" % node.k,end = "")

```

## 测试

```python
t = Tree()
for i in range(10):
	t.add(i)
```
