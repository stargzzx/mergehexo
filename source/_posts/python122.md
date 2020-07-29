---
title: python | collections
date: 2020-07-28 23:06:19
categories:
- python
- 模块
- collections
tags:
- collections
---
有的时候， python 的基础数据结构并不够用，这个时候，可以使用自带的 collections 里面的数据结构。

<!-- more -->

<br/>

# 数组

<br/>

## deque 双端队列

deque 是一个双端队列, 如果要经常从两端append 的数据, 选择这个数据结构就比较好了, 如果要实现随机访问,不建议用这个,请用列表.

deque 优势就是可以从两边append ,appendleft 数据. 这一点list 是没有的.

```python
from collections import deque

#  可以指定 队列的长度
mydeque = deque(maxlen=10)
print(mydeque.maxlen)

# 默认从右边加入
mydeque.append(10)
mydeque.append(12)
print(mydeque)
    # deque([10, 12], maxlen=10)

# 也可以从左边加入
mydeque.appendleft('a')
mydeque.appendleft('b')
print(mydeque)
    # deque(['b', 'a', 10, 12], maxlen=10)

mylist = range(5, 8)
# 也可以加入一个列表，默认从右边加入
mydeque.extend(mylist)
# deque(['b', 'a', 10, 12, 5, 6, 7], maxlen=10)
# 也可以从左边加入
mydeque.extendleft(mylist)
print(mydeque)
    # deque([7, 6, 5, 'b', 'a', 10, 12, 5, 6, 7], maxlen=10)

# 出队列,返回出队列的元素
# 可以从左边也可以从右边 出队列
mydeque.pop()
mydeque.popleft()
    # 其 popleft() 方法可达到 O(1) 时间复杂度；列表 list 的 pop(0) 方法时间复杂度为 O(N)。
print(mydeque)
    # deque([6, 5, 'b', 'a', 10, 12, 5, 6], maxlen=10)

# 查看 队列里面元素个数
print(len(mydeque))
# 8

# 统计元素的个数
# 统计a 有几个
print(mydeque.count('a'))
    # 1

# 在某个位置insert 一个元素
# insert(i, x)
# Insert x into the deque at position i.
mydeque.insert(2, 'frank')
print(mydeque)
    # deque([6, 5, 'frank', 'b', 'a', 10, 12, 5, 6], maxlen=10)

#翻转操作
mydeque.reverse()
print(mydeque)
    # deque([6, 5, 12, 10, 'a', 'b', 'frank', 5, 6], maxlen=10)

# remove 移除某个元素
mydeque.remove(10)
print(mydeque)
    # deque([6, 5, 12, 'a', 'b', 'frank', 5, 6], maxlen=10)

# 清空队列元素 clear
mydeque.clear()
print(mydeque)
    # deque([], maxlen=10)

# copy 浅拷贝
mydeque.copy()
```

`rotate` 方法移动到最后一个，占用第一个位置，循环移动， `value` 是步长`rotate（value)` 对队列实行旋转操作（每个元素依次向后移动`value`步，最后一个移动到第一个算一步）

```python
from collections import deque


d = deque()
d.extend(['a', 'b', 'c', 'd', 'e'])
d.rotate(2)  # 指定次数，默认1次
print(d) 
	# deque(['d', 'e', 'a', 'b', 'c'])
	# 解读
	# d 下标是 3 向后移动两个，由于是循环，所以，下标为 0
	# a 下标是 0 向后移动两个，所以，下标变成 2
```

`maxlen` 要说明一下, 如果指定了 `maxlen` 如果构建`deque` 的时候,指定了`maxlen`, 则可以通过 `d.maxlen` 来获得dueue的最大长度.

如果插入的数据大于 `maxlen` 则会自动删除旧的元素.删除 什么元素,取决于, 从哪边添加数据.

来看一下例子.

```python
d = deque(list(range(5)),maxlen=5)
d.maxlen
	# 5

# 从左边添加元素, # 元素4 被挤出 队列

d.appendleft('frank')
	# deque(['frank', 0, 1, 2, 3])

# 从右边添加元素, 元素 'frank'  被挤出队列.
deque(['frank', 0, 1, 2, 3])
d.append('xiaoming')
	# deque([0, 1, 2, 3, 'xiaoming'])
```

<br/>

# 字典

<br/>

## OrderedDict 有序字典

- 在`Python 3.5`（含）以前，字典是不能保证顺序的，所以，要用 `OrderedDict` 。
- 但是从`Python 3.6`开始，字典是变成有顺序的了。

```python
from collections import OrderedDict

dic = OrderedDict()
dic['k1'] = 'v1'
dic['k2'] = 'v2'
dic['k3'] = 'v3'
print(dic)
    # OrderedDict([('k1', 'v1'), ('k2', 'v2'), ('k3', 'v3')])

# 清空
dic.clear()
print(dic)
# OrderedDict()

# 浅拷贝
dic.copy()

# fromkeys(指定一个列表，把列表中的值作为字典的key,生成一个字典)
name = ['tom', 'lucy', 'sam']
a = dic.fromkeys(name)
d = dic.fromkeys(name, '2')
print(a)
    # OrderedDict([('tom', None), ('lucy', None), ('sam', None)])
print(d)
    # OrderedDict([('tom', '2'), ('lucy', '2'), ('sam', '2')])

# items(返回由“键值对组成元素“的列表)

for key, item in d.items():
    print(key)
    print(item)
    # tom
    # 2
    # lucy
    # 2
    # sam
    # 2

# keys(获取字典所有的key)
print(d.keys())
    # odict_keys(['tom', 'lucy', 'sam'])

# move_to_end(指定一个key，把对应的key-value移到最后)
d.move_to_end('lucy')
print(d)
    # OrderedDict([('tom', '2'), ('sam', '2'), ('lucy', '2')])

# pop(获取指定key的value，并在字典中删除)
print(d.pop('lucy'))
# 2
print(d)
    # OrderedDict([('tom', '2'), ('sam', '2')])

# popitem(按照后进先出原则，删除最后加入的元素，返回key-value)
print(d)
# OrderedDict([('tom', '2'), ('sam', '2')])
print(d.popitem())
# ('sam', '2')
print(d)
    # OrderedDict([('tom', '2')])

# setdefault(获取指定key的value，如果key不存在，则创建)
d.setdefault('l', 5)
print(d)
    # OrderedDict([('tom', '2'), ('l', 5)])

# values(获取字典所有的value，返回一个列表)
print(d.values())
    # odict_values(['2', 5])
```