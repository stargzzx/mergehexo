---
title: trade space for speed
date: 2018-07-03 11:55:58
categories:
- practice
- 算法练习
tags:
- practice
- algorithm
---
Sometimes,We need to deal with array.Like checking target whether it in array or not.We usually use loop to solve,if we just only one layer of loop,it will very efficient,run time about O(n).But if we have more layers,it will run more time,maybe O(n^2) or other.

<!-- more -->

So i will introduce a way to solve this problem in specific environment.

Map

Map is a data-struction.Its appearance like this:

```python
a = {
"key1" : value1,
"key2" : value2
}
```

Map is a hash table.We can find target in near constant time.

Now I will use traditional way and map way to deal same question.Finally i will show their run time.

Question:

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:

```python
Given nums                = [2, 7, 11, 15], target = 9,
Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].
```
I use python3.

The test data:

[3,2,4] target:6

[3,3]   target:6

Traditional way:

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        i = 0;
        j = 1;
        for i in range(len(nums)):
            for j in range(i + 1,len(nums)):
                if(nums[j] == target - nums[i]):
                    return [i,j]
```

Map way:

I made

```python
class Solution:
    def twoSum(self, nums, target):
        """
        :type nums: List[int]
        :type target: int
        :rtype: List[int]
        """
        map    = {}
        length = len(nums)
        for i in range(length):
            map[i] = nums[i]
        for j in range(length):
            number = target - nums[j]
            if (number in list(map.values())[j+1:]):
                return [j,list(map.values())[j+1:].index(number) + j + 1]
```

But,After running,the runtime of traditional way is less than map way. Maybe beause of input data or my code is bad struction and so on.

When I see other very excellent person writed code.That is amazing.His code is art.So i post up there.

```python
class Solution:
        def twoSum(self, nums, target):
        d = {}
        for i, n in enumerate(nums):
            m = target - n
            if m in d:
                return [d[m], i]
            else:
                d[n] = i
```

I hope somedays i can code art.

