---
title: offer 59 - I. 滑动窗口的最大值
date: 2020-07-27 23:20:56
categories:
- 算法
- leetcode
- 简单
tags:
- leetcode
---
记录每一次自己刷题，今日完成任务 1/3 。已优化

<!-- more -->

使用

- python3

<br/>

# 题目描述

<br/>

- [offer 59 - I. 滑动窗口的最大值](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/)

给定一个数组 nums 和滑动窗口的大小 k，请找出所有滑动窗口里的最大值。

示例:

输入: `nums = [1,3,-1,-3,5,3,6,7]`, 和 `k = 3`

输出: `[3,3,5,5,6,7]`

解释: 

	  滑动窗口的位置                最大值
	---------------               -----
	[1  3  -1] -3  5  3  6  7       3
	 1 [3  -1  -3] 5  3  6  7       3
	 1  3 [-1  -3  5] 3  6  7       5
	 1  3  -1 [-3  5  3] 6  7       5
	 1  3  -1  -3 [5  3  6] 7       6
	 1  3  -1  -3  5 [3  6  7]      7
 

提示：

- 你可以假设 k 总是有效的，在输入数组不为空的情况下，1 ≤ k ≤ 输入数组的大小。

<br/>

# 自己的解法

<br/>

## 解法一

- 执行用时：928 ms 击败了 5.28% 的用户
- 内存消耗：17.1 MB 击败了 100% 的用户

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        max_list = []
        i,j = 0,k - 1
        l = len(nums)
        if l == 0:
            return max_list
        while(j < l):
            max = self.get_max(nums[i:j + 1])
            i += 1
            j += 1
            max_list.append(max)
        return max_list
            
    def get_max(self,nums):
        max = nums[0]
        for n in nums[1:]:
            if n > max:
                max = n
        return max
```

上面这个方法大部分人都能想出来。

## 解法二

这个有点讨巧。

- 执行用时：512 ms 击败了 43.78% 的用户
- 内存消耗：17.2 MB 击败 100% 的用户

```python
class Solution:
    def maxSlidingWindow(self, nums: List[int], k: int) -> List[int]:
        max_map = {}

        max_list = []
        i,j = 0,k - 1
        l = len(nums)
        if l == 0:
            return max_list
        while(j < l):
            t = max(nums[i:j + 1])
            i += 1
            j += 1
            max_list.append(t)
        return max_list
```

## 解法三

- 执行用时：72 ms 击败了 83.58% 的用户
- 内存消耗：21.3 MB 击败 100% 的用户

```python
class Solution:
    def maxSlidingWindow(self, nums, k):
        max_map = {}

        max_list = []
        i, j = 0, k - 1
        index = -1
        l = len(nums)
        if l == 0:
            return max_list
        while (j < l):
            if i > index:
                max, t = self.get_max(nums[i:j + 1])
                index = i + t
            else:
                _max = max_map.get(j - 1)
                max = _max if _max > nums[j] else nums[j]
            max_map[j] = max
            i += 1
            j += 1
            max_list.append(max)
        return max_list

    def get_max(self, nums):
        max = nums[0]
        m = 0
        l = len(nums)
        for i in range(l):
            if nums[i] > max:
                max = nums[i]
                m = i
        return max, m
```

这个利用的便是记忆法，记忆一下上一个区间 `[i:j]` 的最大值 `max` 和下标 `index` ，并使用一个 `map` 记录下来

	map = {index : max}

然后，来到下一个区间 `[i + 1 : j + 1]` 这个时候有两个情况

如果，上一个区间的最大值的下标 `index` 不在 这个区间内 `[i + 1 : j + 1]` 的话

即

	index < i + 1

则，最大值 `max_` 应该在 `[i + 1 : j + 1]` 这个区间内得到，从而 `index` 也相应更新

另一个情况是，如果 `index` 在这个区间 `[i + 1 : j + 1]` 内，那么，我们应该比较
	
`max` 和 新加进来的 `j + 1` 下标对应的值，从而，根据情况，选择是否改变 `index` 。

这个利用了便是记忆法。
