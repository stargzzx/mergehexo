---
title: BFS(广度优先)
date: 2018-07-23 22:09:18
categories:
- practice
tags:
- practice
- algorithm
---
其实之前我已经写过一篇关于广度优先的文章了，链接如下。
[]()
<!-- more -->
广度优先是借助了队列，在二叉树中广度优先又叫层次遍历。（？？是不是啊？反正目前的我就是这么理解的，如果有错，容我后期改正，喵喵）
多余的话不再说，只是把代码 copy 一下。(PS：现在我好累啊，真他喵的累)
{% codeblock %}

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
					
{% endcodeblock %}
