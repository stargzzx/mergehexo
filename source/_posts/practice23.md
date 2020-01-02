---
title: 栈的浅析
date: 2018-07-21 21:20:06
categories:
- practice
tags:
- practice
- algorithm
---
这里初度对栈进行浅度分析。
 <!-- more -->
对于栈的定义、原理、特性在这里就不再提了，只是说一下关键点，另外，顺序栈也不说了，就从链栈开始讲起吧。
## 链栈
定义不多说，直接上代码。
{% codeblock %}

class Node():
	def __init__(self,value = None,next = None):
		self.value = value
		self.next = next
	def __str__(self):
		print(self.value)

class LinkList():
	def __init__(self,length = None):
		self.head = None
		self.length = 0
	def add(self,value):
		node = Node(value)
		if(self.head == None):
			self.head = node
			self.length += 1
		else:
			old = self.head
			self.head = node
			node.next = old
			self.length += 1
	def count(self):
		return self.length
	def pop(self):
		if(self.head == None):
			return None
		else:
			node = self.head
			self.head = self.head.next
			self.length -= 1
			return node.value

{% endcodeblock %}
## 测试数据
{% codeblock %}

linkList = LinkList()
linkList.add(12)
linkList.add(11)
print(linkList.count())
print(linkList.pop())
print(linkList.pop())

{% endcodeblock %}