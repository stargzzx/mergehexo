---
title: Offer 32 - I. 从上到下打印二叉树
date: 2020-07-28 16:05:19
categories:
- 算法
- leetcode
- 中等
tags:
- leetcode
---
今天的算法任务。 2/3 已经优化

<!-- more -->

- python3

<br/>

# 题目描述

<br/>

- [Offer 32 - I. 从上到下打印二叉树](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-lcof/)

从上到下打印出二叉树的每个节点，同一层的节点按照从左到右的顺序打印。

例如:

给定二叉树: [3,9,20,null,null,15,7],

	    3
	   / \
	  9  20
	    /  \
	   15   7

返回：

	[3,9,20,15,7]
 

提示：

- 节点总数 <= 1000

<br/>

# 我的解法

<br/>

## 解法一

- 执行用时：52 ms 击败了 16.31% 的用户
- 内存消耗：13.9 MB 击败了 100.00% 的用户

```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[int]:
        q = []
        if root is None:
            return q
        q.append(root)
        result = []
        while(len(q)):
            node = q.pop(0)
            result.append(node.val)
            if(node.left):
                q.append(node.left)
            if(node.right):
                q.append(node.right)
        return result
```

这就是一个层次遍历，用的数据结构是队列。

这里面比较注意的是

	list.pop()
	默认是最后一个元素，要是第一个元素需要 pop(0)
	如果，code 改成 q.pop()

那么，最后这个例子，将会

给定二叉树: [3,9,20,null,null,15,7],

	    3
	   / \
	  9  20
	    /  \
	   15   7

返回：

	[3,20,7,15,9]

## 优化解法一

- 执行用时：40 ms 击败了 81.91% 的用户
- 内存消耗：13.9 MB 击败了 100.00% 的用户

在这里我们使用一个数据结构，双端队列，关于双端队列，你可以查看我下面的博文

- [python | collections](https://benpaodewoniu.github.io/2020/07/28/python122/)

之所以，使用双端队列能够提升速度的原因在于

- 其 popleft() 方法可达到 O(1) 时间复杂度；列表 list 的 pop(0) 方法时间复杂度为 O(N)。

```python
class Solution:
    def levelOrder(self, root: TreeNode) -> List[int]:
        q,dq = [],collections.deque()
        if not root: return q
        dq.append(root)
        while(dq):
            node = dq.popleft()
            q.append(node.val)
            if(node.left):dq.append(node.left)
            if(node.right):dq.append(node.right)
        return q
```