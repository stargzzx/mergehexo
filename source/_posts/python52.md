---
title: python yield 迭代器和生成器
date: 2019-02-20 17:30:54
categories:
- python
tags:
- python
- yield
- 迭代器
- 生成器
---
python 的迭代器和生成器。

<!-- more -->

## 前提

yield 必须放在循环中，要么是 while 要么是 for 循环

## 参考资料

[Python3 迭代器与生成器](http://www.runoob.com/python3/python3-iterator-generator.html)

[Python yield 使用浅析](https://www.ibm.com/developerworks/cn/opensource/os-cn-python-yield/)

## 迭代器

迭代是Python最强大的功能之一，是访问集合元素的一种方式。

迭代器是一个可以记住遍历的位置的对象。

迭代器对象从集合的第一个元素开始访问，直到所有的元素被访问完结束。迭代器只能往前不会后退。

迭代器有两个基本的方法：iter() 和 next()。

字符串，列表或元组对象都可用于创建迭代器：

```python
>>>list=[1,2,3,4]
>>> it = iter(list)    # 创建迭代器对象
>>> print (next(it))   # 输出迭代器的下一个元素
1
>>> print (next(it))
2
>>>		
```

迭代器对象可以使用常规for语句进行遍历：

```python
#!/usr/bin/python3
 
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
for x in it:
    print (x, end=" ")		
```
执行以上程序，输出结果如下：

	1 2 3 4
	
也可以使用 next() 函数：

```python
#!/usr/bin/python3
 
import sys         # 引入 sys 模块
 
list=[1,2,3,4]
it = iter(list)    # 创建迭代器对象
 
while True:
    try:
        print (next(it))
    except StopIteration:
        sys.exit()			
```

执行以上程序，输出结果如下：

	1
	2
	3
	4
	
## 创建一个迭代器

把一个类作为一个迭代器使用需要在类中实现两个方法 __iter__() 与 __next__() 。

如果你已经了解的面向对象编程，就知道类都有一个构造函数，Python 的构造函数为 __init__(), 它会在对象初始化的时候执行。

更多内容查阅：Python3 面向对象

__iter__() 方法返回一个特殊的迭代器对象， 这个迭代器对象实现了 __next__() 方法并通过 StopIteration 异常标识迭代的完成。

__next__() 方法（Python 2 里是 next()）会返回下一个迭代器对象。

创建一个返回数字的迭代器，初始值为 1，逐步递增 1：

```python
class MyNumbers:
  def __iter__(self):
    self.a = 1
    return self
 
  def __next__(self):
    x = self.a
    self.a += 1
    return x
 
myclass = MyNumbers()
myiter = iter(myclass)
 
print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))
print(next(myiter))	
```

执行输出结果为：

	1
	2
	3
	4
	5

```python
class Fab(object):

    def __init__(self, max):
        self.max = max
        self.n, self.a, self.b = 0, 0, 1

    def __iter__(self):
        return self

    def __next__(self):
        if self.n < self.max:
            r = self.b
            self.a, self.b = self.b, self.a + self.b
            self.n = self.n + 1
            return r
        raise StopIteration()

F = Fab(10)
e = iter(F)
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))		
```

执行输出结果为：

	1
	1
	2
	3
	5
	8
	13
	21
	
## StopIteration

StopIteration 异常用于标识迭代的完成，防止出现无限循环的情况，在 __next__() 方法中我们可以设置在完成指定循环次数后触发 StopIteration 异常来结束迭代。

在 20 次迭代后停止执行：

```python
class MyNumbers:
  def __iter__(self):
    self.a = 1
    return self
 
  def __next__(self):
    if self.a <= 20:
      x = self.a
      self.a += 1
      return x
    else:
      raise StopIteration
 
myclass = MyNumbers()
myiter = iter(myclass)
 
for x in myiter:
  print(x)
```

执行输出结果为：

	1
	2
	3
	4
	5
	6
	7
	8
	9
	10
	11
	12
	13
	14
	15
	16
	17
	18
	19
	20

```python
class Fab(object):

    def __init__(self, max):
        self.max = max
        self.n, self.a, self.b = 0, 0, 1

    def __iter__(self):
        return self

    def __next__(self):
        if self.n < self.max:
            r = self.b
            self.a, self.b = self.b, self.a + self.b
            self.n = self.n + 1
            return r
        raise StopIteration()

F = Fab(5)
e = iter(F)
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))
print(next(e))	
```

执行输出结果为：

	1
	1
	2
	3
	5
	Traceback (most recent call last):
	  File "code.py", line 25, in <module>
		print(next(e))
	  File "code.py", line 16, in __next__
		raise StopIteration()
	StopIteration

因为向 Fab 中传递的是 5 ，所以说当执行的迭代次数超过 5 的时候，就会报错。

## 生成器

在 Python 中，使用了 yield 的函数被称为生成器（generator）。

跟普通函数不同的是，生成器是一个返回迭代器的函数，只能用于迭代操作，更简单点理解生成器就是一个迭代器。

在调用生成器运行的过程中，每次遇到 yield 时函数会暂停并保存当前所有的运行信息，返回 yield 的值, 并在下一次执行 next() 方法时从当前位置继续运行。

调用一个生成器函数，返回的是一个迭代器对象。

以下实例使用 yield 实现斐波那契数列：

```python
#!/usr/bin/python3
 
import sys
 
def fibonacci(n): # 生成器函数 - 斐波那契
    a, b, counter = 0, 1, 0
    while True:
        if (counter > n): 
            return
        yield a
        a, b = b, a + b
        counter += 1
f = fibonacci(10) # f 是一个迭代器，由生成器返回生成
 
while True:
    try:
        print (next(f), end=" ")
    except StopIteration:
        sys.exit()	
```

执行以上程序，输出结果如下：

	0 1 1 2 3 5 8 13 21 34 55
	
## 用例子说明 yield 的发展

您可能听说过，带有 yield 的函数在 Python 中被称之为 generator（生成器），何谓 generator ？

我们先抛开 generator，以一个常见的编程题目来展示 yield 的概念。

## 如何生成斐波那契數列

斐波那契（Fibonacci）數列是一个非常简单的递归数列，除第一个和第二个数外，任意一个数都可由前两个数相加得到。用计算机程序输出斐波那契數列的前 N 个数是一个非常简单的问题，许多初学者都可以轻易写出如下函数：

### 清单 1. 简单输出斐波那契數列前 N 个数

	def fab(max): 
	   n, a, b = 0, 0, 1 
	   while n < max: 
		   print b 
		   a, b = b, a + b 
		   n = n + 1
	   
执行 fab(5)，我们可以得到如下输出：

	>>> fab(5) 
	1 
	1 
	2 
	3 
	5

结果没有问题，但有经验的开发者会指出，直接在 fab 函数中用 print 打印数字会导致该函数可复用性较差，因为 fab 函数返回 None，其他函数无法获得该函数生成的数列。

要提高 fab 函数的可复用性，最好不要直接打印出数列，而是返回一个 List。以下是 fab 函数改写后的第二个版本：

### 清单 2. 输出斐波那契數列前 N 个数第二版

	def fab(max): 
	   n, a, b = 0, 0, 1 
	   L = [] 
	   while n < max: 
		   L.append(b) 
		   a, b = b, a + b 
		   n = n + 1 
	   return L

可以使用如下方式打印出 fab 函数返回的 List：

	>>> for n in fab(5): 
	...     print n 
	... 
	1 
	1 
	2 
	3 
	5

改写后的 fab 函数通过返回 List 能满足复用性的要求，但是更有经验的开发者会指出，该函数在运行中占用的内存会随着参数 max 的增大而增大，如果要控制内存占用，最好不要用 List

来保存中间结果，而是通过 iterable 对象来迭代。例如，在 Python2.x 中，代码：

### 清单 3. 通过 iterable 对象来迭代

	for i in range(1000): pass
	
会导致生成一个 1000 个元素的 List，而代码：
	
	for i in xrange(1000): pass
	
则不会生成一个 1000 个元素的 List，而是在每次迭代中返回下一个数值，内存空间占用很小。因为 xrange 不返回 List，而是返回一个 iterable 对象。

利用 iterable 我们可以把 fab 函数改写为一个支持 iterable 的 class，以下是第三个版本的 Fab：

### 清单 4. 第三个版本

	class Fab(object): 
	 
	   def __init__(self, max): 
		   self.max = max 
		   self.n, self.a, self.b = 0, 0, 1 
	 
	   def __iter__(self): 
		   return self 
	 
	   def next(self): 
		   if self.n < self.max: 
			   r = self.b 
			   self.a, self.b = self.b, self.a + self.b 
			   self.n = self.n + 1 
			   return r 
		   raise StopIteration()

Fab 类通过 next() 不断返回数列的下一个数，内存占用始终为常数：

	>>> for n in Fab(5): 
	...     print n 
	... 
	1 
	1 
	2 
	3 
	5

然而，使用 class 改写的这个版本，代码远远没有第一版的 fab 函数来得简洁。如果我们想要保持第一版 fab 函数的简洁性，同时又要获得 iterable 的效果，yield 就派上用场了：

### 清单 5. 使用 yield 的第四版

	def fab(max): 
		n, a, b = 0, 0, 1 
		while n < max: 
			yield b 
			# print b 
			a, b = b, a + b 
			n = n + 1 
	 
	'''

第四个版本的 fab 和第一版相比，仅仅把 print b 改为了 yield b，就在保持简洁性的同时获得了 iterable 的效果。

调用第四版的 fab 和第二版的 fab 完全一致：

	>>> for n in fab(5): 
	...     print n 
	... 
	1 
	1 
	2 
	3 
	5

简单地讲，yield 的作用就是把一个函数变成一个 generator，带有 yield 的函数不再是一个普通函数，Python 解释器会将其视为一个 generator，调用 fab(5) 不会执行 fab 函数，而是返回一个 iterable 对象！在 for 循环执行时，每次循环都会执行 fab 函数内部的代码，执行到 yield b 时，fab 函数就返回一个迭代值，下次迭代时，代码从 yield b 的下一条语句继续执行，而函数的本地变量看起来和上次中断执行前是完全一样的，于是函数继续执行，直到再次遇到 yield。
也可以手动调用 fab(5) 的 next() 方法（因为 fab(5) 是一个 generator 对象，该对象具有 next() 方法），这样我们就可以更清楚地看到 fab 的执行流程：
### 清单 6. 执行流程

	>>> f = fab(5) 
	>>> f.next() 
	1 
	>>> f.next() 
	1 
	>>> f.next() 
	2 
	>>> f.next() 
	3 
	>>> f.next() 
	5 
	>>> f.next() 
	Traceback (most recent call last): 
	 File "<stdin>", line 1, in <module> 
	StopIteration

当函数执行结束时，generator 自动抛出 StopIteration 异常，表示迭代完成。在 for 循环里，无需处理 StopIteration 异常，循环会正常结束。

我们可以得出以下结论：

一个带有 yield 的函数就是一个 generator，它和普通函数不同，生成一个 generator 看起来像函数调用，但不会执行任何函数代码，直到对其调用 next()（在 for 循环中会自动调用 next()）才开始执行。虽然执行流程仍按函数的流程执行，但每执行到一个 yield 语句就会中断，并返回一个迭代值，下次执行时从 yield 的下一个语句继续执行。看起来就好像一个函数在正常执行的过程中被 yield 中断了数次，每次中断都会通过 yield 返回当前的迭代值。

yield 的好处是显而易见的，把一个函数改写为一个 generator 就获得了迭代能力，比起用类的实例保存状态来计算下一个 next() 的值，不仅代码简洁，而且执行流程异常清晰。

如何判断一个函数是否是一个特殊的 generator 函数？可以利用 isgeneratorfunction 判断：

### 清单 7. 使用 isgeneratorfunction 判断

	>>> from inspect import isgeneratorfunction 
	>>> isgeneratorfunction(fab) 
	True

要注意区分 fab 和 fab(5)，fab 是一个 generator function，而 fab(5) 是调用 fab 返回的一个 generator，好比类的定义和类的实例的区别：

### 清单 8. 类的定义和类的实例

	>>> import types 
	>>> isinstance(fab, types.GeneratorType) 
	False 
	>>> isinstance(fab(5), types.GeneratorType) 
	True

fab 是无法迭代的，而 fab(5) 是可迭代的：

	>>> from collections import Iterable 
	>>> isinstance(fab, Iterable) 
	False 
	>>> isinstance(fab(5), Iterable) 
	True

每次调用 fab 函数都会生成一个新的 generator 实例，各实例互不影响：

	>>> f1 = fab(3) 
	>>> f2 = fab(5) 
	>>> print 'f1:', f1.next() 
	f1: 1 
	>>> print 'f2:', f2.next() 
	f2: 1 
	>>> print 'f1:', f1.next() 
	f1: 1 
	>>> print 'f2:', f2.next() 
	f2: 1 
	>>> print 'f1:', f1.next() 
	f1: 2 
	>>> print 'f2:', f2.next() 
	f2: 2 
	>>> print 'f2:', f2.next() 
	f2: 3 
	>>> print 'f2:', f2.next() 
	f2: 5

## return 的作用

在一个 generator function 中，如果没有 return，则默认执行至函数完毕，如果在执行过程中 return，则直接抛出 StopIteration 终止迭代。

## 另一个例子

另一个 yield 的例子来源于文件读取。如果直接对文件对象调用 read() 方法，会导致不可预测的内存占用。好的方法是利用固定长度的缓冲区来不断读取文件内容。通过 yield，我们不再需要编写读文件的迭代类，就可以轻松实现文件读取：

清单 9. 另一个 yield 的例子

	def read_file(fpath): 
	   BLOCK_SIZE = 1024 
	   with open(fpath, 'rb') as f: 
		   while True: 
			   block = f.read(BLOCK_SIZE) 
			   if block: 
				   yield block 
			   else: 
				   return
				   
以上仅仅简单介绍了 yield 的基本概念和用法，yield 在 Python 3 中还有更强大的用法，我们会在后续文章中讨论。

注：本文的代码均在 Python 2.7 中调试通过

## 我的代码示例

### 叠加 yield

在这个方法中我们还能调用方法

#### 没有 while 的例子

```python
def a():
    print("a")

def b():
    for i in range(1):
        x = 0
        y = 0
        for i in range(5):
            x += 1
            yield x
        print(123)
        for k in range(3):
            yield a()
			
f = b()
print(next(f))
print(next(f))
print(next(f))
print(next(f))
print(next(f))
next(f)
next(f)
next(f)
next(f)
next(f)
next(f)
next(f)		
```

输出

```python
1
2
3
4
5
123
a
a
a
Traceback (most recent call last):
  File "ski.py", line 25, in <module>
    next(f)
StopIteration
```

因为 next 的输出超过了循环中的次数

#### 有 while 的代码

```python
def a():
    print("a")

def b():
    while(True):
        x = 0
        y = 0
        for i in range(5):
            x += 1
            yield x
        print(123)
        for k in range(3):
            yield a()


f = b()
print(next(f))
print(next(f))
print(next(f))
print(next(f))
print(next(f))
next(f)
next(f)
next(f)
next(f)
next(f)
next(f)
next(f)

```
输出
```python
1
2
3
4
5
123
a
a
a
```

while 的是不出错的

