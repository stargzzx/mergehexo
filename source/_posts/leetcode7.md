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
今天算法任务。 2 / 3 已经优化

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

<br/>

# leetcode

<br/>

## 解法一

<div style="width: 50%;padding-left: 25%">

![](/images/leetcode/7_0.png)

</div>

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        res = 0
        for i in range(32):
            cnt = 0  # 记录当前 bit 有多少个1
            bit = 1 << i  # 记录当前要操作的 bit
            for num in nums:
                if num & bit != 0:
                    cnt += 1
            if cnt % 3 != 0:
                # 不等于0说明唯一出现的数字在这个 bit 上是1
                res |= bit

        return res - 2 ** 32 if res > 2 ** 31 - 1 else res
```

- 为什么Python最后需要对返回值进行判断？

如果不这么做的话测试用例是`[-2,-2,1,1,-3,1,-3,-3,-4,-2]` 的时候，就会输出 `4294967292`。 其原因在于`Python`是动态类型语言，在这种情况下其会将符号位置的`1`看成了值，而不是当作符号“负数”。 这是不对的。 正确答案应该是 `-4`，`-4`的二进制码是 `1111...100`，就变成 `2^32-4=4294967292`，解决办法就是 减去 `2 ** 32` 。

>之所以这样不会有问题的原因还在于题目限定的数组范围不会超过 `2 ** 32`
