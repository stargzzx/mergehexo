---
title: 动态规划
date: 2020-08-07 14:27:58
categories:
- 算法
- 经典
tags:
- 算法
---
何为动态规划？起这么牛逼哄哄的例子，听着就有点让人劝退的感觉。

就我目前的理解来说，动态规划，有一个基础值，而后面的值可以从前面的值进行演变，在后面的值，又可以通过前面的值演变出来的值，进行演变。

这么听起来是不是有点像 “记忆化” 。动态规划确实是记忆化，但是，和记忆化不同的是，其空间复杂度是 O(1)。

<!-- more -->

我将以两道算法题为例，来讲述动态规划。

- [动态规划套路详解](https://zhuanlan.zhihu.com/p/78220312)

我也推荐你看一下，我之前写的博客。

- [算法 | 斐波那契与记忆性](https://benpaodewoniu.github.io/2020/06/16/algorithm37/)

<br/>

# 动态规划套路

<br/>

- 明确基本问题
- 最优子结构
- 传递方程
- 尝试记忆化
	- 解决重叠子问题
- 尝试优化记忆化空间

<br/>

# 斐波那契函数

<br/>

斐波那契函数，严格意义上来讲并不是一个动态规划问题，因为，动态规划有极值，而斐波那契是没有极值的。

在这里假设你看了我那那篇博文，并且，了解到了斐波那契函数的记忆化，现在将那段代码摘抄进来。

```python
from typing import Dict


memo: Dict[int, int] = {0: 0, 1: 1}    # base cases

def fib(n: int) -> int:
    if n not in memo:
        memo[n] = fib3(n - 1) + fib3(n - 2)    # memoization
    return memo[n]


if __name__ == '__main__':
    print(fib(5))
    print(fib(50))
```

在这里，我们先不将其改编，而是，明确套路中代表的各项含义。

- 明确基本问题
	- fib(0) = 0
	- fib(1) = 1
- 最优子结构
	- fib(n)
- 传递方程
	- fib(n) = fib(n - 1) + fib(n - 2)
- 尝试记忆化
	- 解决重叠子问题
	- memo = dict()
- 尝试优化记忆化空间

接下来，我们就要解决记忆化的优化空间了。

我们从传递方程入手

	fib(n) = fib(n - 1) + fib(n - 2)

`fib(n)` 的值，也只是需要两个值而已，但是，`memo` 却把所有的值都存起来了，那么，我们只需要两个临时变量就可以替代 `memo` 了，于是，写出下面的代码

```python
def fib(n: int) -> int:
    res = 0
    pre = 0
    _pre = 1
    for i in range(n - 2):
        res = pre + _pre
        pre = _pre
        _pre = res
    return res
```

当然，上面的代码，有点 `bug` ，没有考虑 `0` 和 `1` 的情况。不过，也说明了如何优化。

<br/>

# 零钱问题

<br/>

