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

## defaultdict 默认返回字典

- [python中defaultdict方法的使用](https://blog.csdn.net/u010700415/article/details/17919289)

### 默认值可以很方便

众所周知，在`Python`中如果访问字典中不存在的键，会引发`KeyError`异常（JavaScript中如果对象中不存在某个属性，则返回undefined）。但是有时候，字典中的每个键都存在默认值是非常方便的。例如下面的例子：

```python
strings = ('puppy', 'kitten', 'puppy', 'puppy',
           'weasel', 'puppy', 'kitten', 'puppy')
counts = {}
 
for kw in strings:
    counts[kw] += 1
```

该例子统计`strings`中某个单词出现的次数，并在`counts`字典中作记录。单词每出现一次，在`count`相对应的键所存的值数字加1。但是事实上，运行这段代码会抛出`KeyError`异常，出现的时机是每个单词第一次统计的时候，因为`Python`的`dict`中不存在默认值的说法，可以在`Python`命令行中验证：

```python
>>> counts = dict()
>>> counts
{}
>>> counts['puppy'] += 1
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'puppy'
```

### 使用判断语句检查

既然如此，首先可能想到的方法是在单词第一次统计的时候，在`counts`中相应的键存下默认值`1`。这需要在处理的时候添加一个判断语句：

```python
strings = ('puppy', 'kitten', 'puppy', 'puppy',
           'weasel', 'puppy', 'kitten', 'puppy')
counts = {}
 
for kw in strings:
    if kw not in counts:
        counts[kw] = 1
    else:
        counts[kw] += 1
 
# counts:
# {'puppy': 5, 'weasel': 1, 'kitten': 2}
```

### 使用dict.setdefault()方法

也可以通过`dict.setdefault()`方法来设置默认值：

```python
strings = ('puppy', 'kitten', 'puppy', 'puppy',
           'weasel', 'puppy', 'kitten', 'puppy')
counts = {}
 
for kw in strings:
    counts.setdefault(kw, 0)
    counts[kw] += 1
```

`dict.setdefault()`方法接收两个参数，第一个参数是健的名称，第二个参数是默认值。假如字典中不存在给定的键，则返回参数中提供的默认值；反之，则返回字典中保存的值。利用`dict.setdefault()`方法的返回值可以重写`for`循环中的代码，使其更加简洁：

```python
strings = ('puppy', 'kitten', 'puppy', 'puppy',
           'weasel', 'puppy', 'kitten', 'puppy')
counts = {}
 
for kw in strings:
    counts[kw] = counts.setdefault(kw, 0) + 1
```

### 使用collections.defaultdict类

以上的方法虽然在一定程度上解决了dict中不存在默认值的问题，但是这时候我们会想，有没有一种字典它本身提供了默认值的功能呢？答案是肯定的，那就是`collections.defaultdict`。

`defaultdict`类就好像是一个`dict`，但是它是使用一个类型来初始化的：

```python
>>> from collections import defaultdict
>>> dd = defaultdict(list)
>>> dd
defaultdict(<type 'list'>, {})
```

`defaultdict`类的初始化函数接受一个类型作为参数，当所访问的键不存在的时候，可以实例化一个值作为默认值：

```python
>>> dd['foo']
[]
>>> dd
defaultdict(<type 'list'>, {'foo': []})
>>> dd['bar'].append('quux')
>>> dd
defaultdict(<type 'list'>, {'foo': [], 'bar': ['quux']})
```

需要注意的是，这种形式的默认值只有在通过`dict[key]`或者`dict.__getitem__(key)`访问的时候才有效，这其中的原因在下文会介绍。

```python
>>> from collections import defaultdict
>>> dd = defaultdict(list)
>>> 'something' in dd
False
>>> dd.pop('something')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 'pop(): dictionary is empty'
>>> dd.get('something')
>>> dd['something']
[]
```

`defaultdict`类除了接受类型名称作为初始化函数的参数之外，还可以使用任何不带参数的可调用函数，到时该函数的返回结果作为默认值，这样使得默认值的取值更加灵活。下面用一个例子来说明，如何用自定义的不带参数的函数`zero()`作为`defaultdict`类的初始化函数的参数：

```python
>>> from collections import defaultdict
>>> def zero():
...     return 0
...
>>> dd = defaultdict(zero)
>>> dd
defaultdict(<function zero at 0xb7ed2684>, {})
>>> dd['foo']
0
>>> dd
defaultdict(<function zero at 0xb7ed2684>, {'foo': 0})
```

利用`collections.defaultdict`来解决最初的单词统计问题，代码如下：

```python
from collections import defaultdict
 
strings = ('puppy', 'kitten', 'puppy', 'puppy',
           'weasel', 'puppy', 'kitten', 'puppy')
counts = defaultdict(lambda: 0)  # 使用lambda来定义简单的函数
 
for s in strings:
    counts[s] += 1
```

### defaultdict类是如何实现的

通过上面的内容，想必大家已经了解了`defaultdict`类的用法，那么在`defaultdict`类中又是如何来实现默认值的功能呢？这其中的关键是使用了看`__missing__()`这个方法：

```python
>>> from collections import defaultdict
>>> print defaultdict.__missing__.__doc__
__missing__(key) # Called by __getitem__ for missing key; pseudo-code:
  if self.default_factory is None: raise KeyError(key)
  self[key] = value = self.default_factory()
  return value
```

通过查看`__missing__()`方法的`docstring`，可以看出当使用`__getitem__()`方法访问一个不存在的键时(`dict[key]`这种形式实际上是`__getitem__()`方法的简化形式)，会调用`__missing__()`方法获取默认值，并将该键添加到字典中去。

关于`__missing__()`方法的具体介绍可以参考`Python`官方文档中的"`Mapping Types — dict`"一节。

文档中介绍，从2.5版本开始，如果派生自dict的子类定义了`__missing__()`方法，当访问不存在的键时，`dict[key]`会调用`__missing__()`方法取得默认值。

从中可以看出，虽然dict支持`__missing__()`方法，但是在dict本身是不存在这个方法的，而是需要在派生的子类中自行实现这个方法。可以简单的验证这一点：

```python
>>> print dict.__missing__.__doc__
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: type object 'dict' has no attribute '__missing__'
同时，我们可以进一步的做实验，定义一个子类Missing并实现__missing__()方法:

>>> class Missing(dict):
...     def __missing__(self, key):
...         return 'missing'
...
>>> d = Missing()
>>> d
{}
>>> d['foo']
'missing'
>>> d
{}
```

返回结果反映了`__missing__()`方法确实发挥了作用。在此基础上，我们稍许修改`__missing__()`方法,使得该子类同`defautldict`类一样为不存在的键设置一个默认值：

```python
>>> class Defaulting(dict):
...     def __missing__(self, key):
...         self[key] = 'default'
...         return 'default'
...
>>> d = Defaulting()
>>> d
{}
>>> d['foo']
'default'
>>> d
{'foo': 'default'}
```

### 在旧版本的Python中实现类defaultdict的功能

`defaultdict`类是从`2.5`版本之后才添加的，在一些旧版本中并不支持它，因此为旧版本实现一个兼容的`defaultdict`类是必要的。这其实很简单，虽然性能可能未必如`2.5`版本中自带的`defautldict`类好，但在功能上是一样的。

首先，`__getitem__()`方法需要在访问键失败时，调用`__missing__()`方法：

```python
class defaultdict(dict):
    def __getitem__(self, key):
        try:
            return dict.__getitem__(self, key)
        except KeyError:
            return self.__missing__(key)
```

其次，需要实现`__missing__()`方法用来设置默认值：

```python
class defaultdict(dict):
    def __getitem__(self, key):
        try:
            return dict.__getitem__(self, key)
        except KeyError:
            return self.__missing__(key)
 
    def __missing__(self, key):
        self[key] = value = self.default_factory()
        return value
```

然后，`defaultdict`类的初始化函数`__init__()`需要接受类型或者可调用函数参数:

```python
class defaultdict(dict):
    def __init__(self, default_factory=None, *a, **kw):
        dict.__init__(self, *a, **kw)
        self.default_factory = default_factory
 
    def __getitem__(self, key):
        try:
            return dict.__getitem__(self, key)
        except KeyError:
            return self.__missing__(key)
 
    def __missing__(self, key):
        self[key] = value = self.default_factory()
        return value
```
最后，综合以上内容，通过以下方式完成兼容新旧Python版本的代码：

```python
try:
    from collections import defaultdict
except ImportError:
    class defaultdict(dict):
      def __init__(self, default_factory=None, *a, **kw):
          dict.__init__(self, *a, **kw)
          self.default_factory = default_factory
 
      def __getitem__(self, key):
          try:
              return dict.__getitem__(self, key)
          except KeyError:
              return self.__missing__(key)
 
      def __missing__(self, key):
          self[key] = value = self.default_factory()
          return value
```