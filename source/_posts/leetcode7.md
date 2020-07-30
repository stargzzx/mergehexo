---
title: Offer 56 - II. 数组中数字出现的次数 II
date: 2020-07-29 21:08:21
categories:
- 算法
- leetcode
- 中等
tags:
- leetcode
---
今天算法任务。 2 / 3

<!-- more -->

<br/>

# 题目描述

<br/>

- [Offer 56 - II. 数组中数字出现的次数 II](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

在一个数组 `nums` 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

示例 1：

	输入：nums = [3,4,3,3]
	输出：4

示例 2：

	输入：nums = [9,1,7,9,7,9,7]
	输出：1
 

限制：

- 1 <= nums.length <= 10000
- 1 <= nums[i] < 2^31

<br/>

# 我的解法

<br/>

## 解法一

- 执行用时：1216 ms 击败了 5.00% 的用户
- 内存消耗：14.9 MB 击败了 100.00% 的用户

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        num_list = {-1:[]}
        for i in nums:
            if not (num_list.get(i,None)):
                if i in num_list[-1]:
                    num_list[i] = 1
                    num_list[-1].remove(i)
                else:
                    num_list[-1].append(i)
        return num_list[-1][0]
```

## 解法二

上面最费时间的就是

	num_list[-1].remove(i)

删除之后，因为要移动组内元素，所以，时间复杂度是 O(n).

这个时候，我们可以使用字典，因为，字典的去除是 O(1)。

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        num_list = {-1:{}}
        for i in nums:
            if not (num_list.get(i,None)):
                if i in num_list[-1].keys():
                    num_list[i] = 1
                    num_list[-1].pop(i)
                else:
                    num_list[-1][i] = 1
        return list(num_list[-1].keys())[0]
```

不过，上述两种解法没有本质上的区别。


