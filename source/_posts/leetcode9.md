---
title: Offer 22. 链表中倒数第k个节点
date: 2020-08-06 13:29:58
categories:
- 算法
- leetcode
- 简单
tags:
- leetcode
---
今日算法题。 2/3.

<!-- more -->

- python3
- [Offer 22. 链表中倒数第k个节点](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

<br/>

# 题目描述

<br/>

输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。例如，一个链表有6个节点，从头节点开始，它们的值依次是1、2、3、4、5、6。这个链表的倒数第3个节点是值为4的节点。

示例：

	给定一个链表: 1->2->3->4->5, 和 k = 2.
	返回链表 4->5.

<br/>

# 我的解法

<br/>

## 解法一

- 执行用时：44 ms, 击败了 57.24% 的用户
- 内存消耗：13.6 MB, 击败了 84.34% 的用户

```python
class Solution:
    def getKthFromEnd(self, head: ListNode, k: int) -> ListNode:
        i,j = head,head
        for _ in range(k - 1):
            i = i.next
        while(i.next):
            i = i.next
            j = j.next
        return j
```

