---
title: Offer 52. 两个链表的第一个公共节点
date: 2020-08-11 14:40:59
categories:
- 算法
- leetcode
- 简单
tags:
- leetcode
---
今日算法题。 2/3.

<!-- more -->

输入两个链表，找出它们的第一个公共节点。

如下面的两个链表：

![](/images/leetcode/12_0.png)

在节点 c1 开始相交。
 

示例 1：

![](/images/leetcode/12_1.png)

>输入：intersectVal = 8, listA = [4,1,8,4,5], listB = [5,0,1,8,4,5], skipA = 2, skipB = 3
输出：Reference of the node with value = 8
输入解释：相交节点的值为 8 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [4,1,8,4,5]，链表 B 为 [5,0,1,8,4,5]。在 A 中，相交节点前有 2 个节点；在 B 中，相交节点前有 3 个节点。
 

示例 2：

![](/images/leetcode/12_2.png)

>输入：intersectVal = 2, listA = [0,9,1,2,4], listB = [3,2,4], skipA = 3, skipB = 1
输出：Reference of the node with value = 2
输入解释：相交节点的值为 2 （注意，如果两个列表相交则不能为 0）。从各自的表头开始算起，链表 A 为 [0,9,1,2,4]，链表 B 为 [3,2,4]。在 A 中，相交节点前有 3 个节点；在 B 中，相交节点前有 1 个节点。
 

示例 3：

![](/images/leetcode/12_3.png)

>输入：intersectVal = 0, listA = [2,6,4], listB = [1,5], skipA = 3, skipB = 2
输出：null
输入解释：从各自的表头开始算起，链表 A 为 [2,6,4]，链表 B 为 [1,5]。由于这两个链表不相交，所以 intersectVal 必须为 0，而 skipA 和 skipB 可以是任意值。
解释：这两个链表不相交，因此返回 null。
 

注意：

- 如果两个链表没有交点，返回 null.
- 在返回结果后，两个链表仍须保持原有的结构。
- 可假定整个链表结构中没有循环。
- 程序尽量满足 O(n) 时间复杂度，且仅用 O(1) 内存。

这里我要特意解释一下，为什么 `示例1` 中的公共节点不是 1 而是 8 ，公共节点不仅要求值相同，还要求引用相同。

<br/>

# 我的解法

<br/>

在看解答之前，我推荐你去看一下，下面的博文。

- [python | 值比较和引用比较](https://benpaodewoniu.github.io/2020/08/11/python125/)

## 解法一

- 运行时间 204ms 击败了 29.41%
- 内存消耗 29 MB 击败了 16.05%

```python
class Solution:
    def getIntersectionNode(self, headA: ListNode, headB: ListNode) -> ListNode:
        if headB is None or headA is None:
            return None
        node_a,node_b = headA,headB
        list_a = []
        list_b = []
        while(node_a):
            list_a.append(node_a)
            node_a = node_a.next
        while(node_b):
            list_b.append(node_b)
            node_b = node_b.next
        a,b = list_a.pop(),list_b.pop()
        tmp = None
        while(a is b and a and b):
            tmp = a
            if (len(list_a) > 0):
                a = list_a.pop()
            else:
                a = None
            if (len(list_b) > 0):
                b = list_b.pop()
            else:
                b = None
        return tmp
```