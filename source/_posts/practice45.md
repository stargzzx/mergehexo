---
title: practice45
date: 2018-08-15 11:56:54
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：
用非递归手段打印二叉树的先序排列。
<!-- more -->
可以看看我之前的两篇文章。
[二叉树创建、四大遍历](https://benpaodewoniu.github.io/2018/06/30/practice3/)
[递归](https://benpaodewoniu.github.io/2018/06/14/basis2/)
思路就是用 stack 来代替递归。
具体过程：

1. 首先申请一个新的栈，记为 stack
2. 然后将头结点 head 压入 stack 中
3. 每次从 stack 中弹出栈顶节点，记为 cur ，然后打印 cur 节点的值。如果 cur 有孩子不为空的话，将 cur 的右孩子先压入 stack 中。最后如果 cur 的左孩子不为空的话，将 cur 的左孩子压入 stack 中。
中序和后序以此可以推出。

中序：

1. 申请一个栈，记为 stcak ，申请一个变量 cur ，初始 cur 等于 头结点
2. 先把 cur 节点压入栈中，对以 cur 节点为头的整棵子树来说，依次把整棵树的左边界压入栈中，即不断令 cur = cur.left ，重复步骤 2
3. 直到 cur 为空，此时从 stack 中弹出一个节点，记为 node 。打印 node 的值，并 cur = node.right ，然后继续重复步骤 2 。
4. 当 stack 为空的时候，且 cur 也为空，整个过程结束。

后序：

1. 申请一个栈，记为 stack ，将头结点压入 stack ，同时设置两个变量 h 和 c 。在整个流程中， h 代表最近一次弹出并打印的节点。 c 代表当前 stack 的栈顶节点，初始时令 h 为头结点， c 为 NULL。
2. 每次令 c 等于当前 stack 的栈顶节点，但是不从 stack 中弹出节点，此时分为以下三种情况。
（1） 如果 c 的左孩子不为空，并且 h 不等于 c 的左孩子，也不等于 c 的右孩子，则把 c 的左孩子压入 stack 中。
（2） 如果其情况 1 不成立，并且 c 的右孩子不为空，并且 h 不等于 c 的右孩子，则把 c 的右孩子压入 stack 中。
（3） 如果 1 和 2 都不成立，那么 stack 弹出 c 并打印，令 h 等于 c 。

## 代码
{% codeblock %}

class Node:  # 节点类
    def __init__(self, k):
        self.k = k
        self.l = None
        self.r = None


class Tree:  # 二叉树类
    def __init__(self):
        self.root = None

    def add(self, key):  # 添加节点 借助数组构建节点
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

    def preorder(self, node):  # 递归前序遍历
        if node is None:
            print("#!", end="")
            return
        else:
            print("%s!" % node.k, end="")
            self.preorder(node.l)
            self.preorder(node.r)

    def preorder2(self, node):  # 非递归前序遍历
        stack = []
        stack.append(node)
        while(len(stack) is not 0):
            tmp = stack.pop()
            print(tmp.k,end=" ")
            if tmp.r is not None:
                stack.append(tmp.r)
            if tmp.l is not None:
                stack.append(tmp.l)


tree = Tree()
tree.add(1)
tree.add(2)
tree.add(3)
tree.add(4)
tree.add(5)
tree.preorder2(tree.root)

{% endcodeblock %}