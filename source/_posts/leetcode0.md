---
title: leetcode | 337. 打家劫舍 III
date: 2020-05-03 22:27:32
categories:
- 算法
- leetcode
- 中等
tags:
- leetcode
---
这道题蛮有意思的。

<!-- more -->

<br/>

# 题目链接

<br/>

- [打家劫舍 III](https://leetcode-cn.com/problems/house-robber-iii/)

<br/>

# 题目描述

<br/>

在上次打劫完一条街道之后和一圈房屋后，小偷又发现了一个新的可行窃的地区。这个地区只有一个入口，我们称之为“根”。 除了“根”之外，每栋房子有且只有一个“父“房子与之相连。一番侦察之后，聪明的小偷意识到“这个地方的所有房屋的排列类似于一棵二叉树”。 如果两个直接相连的房子在同一天晚上被打劫，房屋将自动报警。

计算在不触动警报的情况下，小偷一晚能够盗取的最高金额。

## 示例 1:

	输入: [3,2,3,null,3,null,1]

	     3
	    / \
	   2   3
	    \   \ 
	     3   1

	输出: 7 
	解释: 小偷一晚能够盗取的最高金额 = 3 + 3 + 1 = 7.

## 示例 2:

	输入: [3,4,5,1,3,null,1]

	     3
	    / \
	   4   5
	  / \   \ 
	 1   3   1

	输出: 9
	解释: 小偷一晚能够盗取的最高金额 = 4 + 5 = 9.

<br/>

# 解题

<br/>

想了很久都没有解题思路，于是就看了一下解析。

- [三种方法解决树形动态规划问题](https://leetcode-cn.com/problems/house-robber-iii/solution/san-chong-fang-fa-jie-jue-shu-xing-dong-tai-gui-hu/)

他的方法如下

- 递归
- 递归 + 记忆化
- 动态规划

不过，我没理解他的动态规划是什么意思。

所以，只做了前两项。使用 python 实现。

## 递归

在看递归之前你可以看我下面的文章

- [递归](https://benpaodewoniu.github.io/2018/06/14/basis2/)

```python
class Solution:
    def rob(self, root: TreeNode) -> int:
        if not root:
            return 0
        money = root.val
        if root.right:
            money += self.rob(root.right.left) + self.rob(root.right.right)
        if root.left:
            money += self.rob(root.left.right) + self.rob(root.left.left)
        return max(money, self.rob(root.right) + self.rob(root.left))
```

很不幸的是， `python` 的运行效率太低上面的题目，在 123/124 用例的时候超时。

## 递归 + 记忆化

在看这个解法的时候，建议看一下

- [算法 | 斐波那契与记忆性](https://benpaodewoniu.github.io/2020/06/16/algorithm37/)

```python
class Solution:
    def rob(self, root) -> int:

        meno = {}
        if not root:
            return 0
        if root in meno:
            return meno[root]

        a = root.val
        if root.left:
            a += self.rob(root.left.left) + self.rob(root.left.right)
        if root.right:
            a += self.rob(root.right.left) + self.rob(root.right.right)

        b = 0
        b += self.rob(root.left) + self.rob(root.right)

        res = max(a, b)
        meno[root] = res
        return res
```

但是，很不幸，这个在 123/124 的时候也是超时。

于是，改用其他的代码

```python
class Solution:
    def rob(self, root: TreeNode) -> int:

        meno = {}

        def dfs(root):
            if not root: return 0
            if root in meno: return meno[root]

            a = root.val
            if root.left:
                a += dfs(root.left.left) + dfs(root.left.right)
            if root.right:
                a += dfs(root.right.left) + dfs(root.right.right)
            
            b = 0
            b += dfs(root.left) + dfs(root.right)

            res = max(a, b)
            meno[root] = res
            return res
        
        return dfs(root)
```

这个代码通过了，看来嵌套函数比自我调用快。


