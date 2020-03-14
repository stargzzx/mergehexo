---
title: python | super()
date: 2020-03-14 13:31:46
categories:
- python
tags:
- python
- super
---
这个方法经常见，但是，不知道具体怎么用，所以，在这里，我进行一次总结。

<!-- more -->

<br/>

# 描述

<br/>

super() 函数是用于调用父类(超类)的一个方法。

super 是用来解决多重继承问题的，直接用类名调用父类方法在使用单继承的时候没问题，但是如果使用多继承，会涉及到查找顺序（MRO）、重复调用（钻石继承）等种种问题。

MRO 就是类的方法解析顺序表, 其实也就是继承父类方法时的顺序表。

<br/>

# 语法

<br/>

以下是 super() 方法的语法:

	super(type[, object-or-type])

- type -- 类。
- object-or-type -- 类，一般是 self

Python3.x 和 Python2.x 的一个区别是: Python 3 可以使用直接使用 super().xxx 代替 super(Class, self).xxx :

## python3.x

```python
class A:
     def add(self, x):
         y = x+1
         print(y)
class B(A):
    def add(self, x):
        super().add(x)
b = B()
b.add(2)  # 3
```

## python2.x

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class A(object):   # Python2.x 记得继承 object
    def add(self, x):
         y = x+1
         print(y)
class B(A):
    def add(self, x):
        super(B, self).add(x)
b = B()
b.add(2)  # 3
```

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class A(object):   # Python2.x 记得继承 object
    def add(self, x):
         y = x+1
         print(y)
class B(A):
    def add(self, x):
        super(B, self).add(x)
b = B()
b.add(2)  # 3
```

<br/>

# 实例

<br/>

在我们的印象中，对于super(B, self).\_\_init\_\_()是这样理解的：super(B, self)首先找到B的父类（就是类A），然后把类B的对象self转换为类A的对象，然后“被转换”的类A对象调用自己的\_\_init\_\_函数。

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
class FooParent(object):
    def __init__(self):
        self.parent = 'I\'m the parent.'
        print ('Parent')
    
    def bar(self,message):
        print ("%s from Parent" % message)
 
class FooChild(FooParent):
    def __init__(self):
        # super(FooChild,self) 首先找到 FooChild 的父类（就是类 FooParent），然后把类 FooChild 的对象转换为类 FooParent 的对象
        super(FooChild,self).__init__()    
        print ('Child')
        
    def bar(self,message):
        super(FooChild, self).bar(message)
        print ('Child bar fuction')
        print (self.parent)
 
if __name__ == '__main__':
    fooChild = FooChild()
    fooChild.bar('HelloWorld')
```

	Parent
	Child
	HelloWorld from Parent
	Child bar fuction
	I'm the parent.

<br/>

# 查找顺序（MRO）、重复调用（钻石继承）

<br/>

super()解决了子类调用父类方法的一些问题， 父类多次被调用时只执行一次， 优化了执行逻辑，下面我们就来详细看一下。

举一个例子：

```python
class Foo:
  def bar(self, message):
    print(message)
>>> Foo().bar("Hello, Python.")
Hello, Python.
```

当存在继承关系的时候，有时候需要在子类中调用父类的方法，此时最简单的方法是把对象调用转换成类调用，需要注意的是这时self参数需要显式传递，例如

```python
class FooParent:
  def bar(self, message):
    print(message)
class FooChild(FooParent):
  def bar(self, message):
    FooParent.bar(self, message)
>>> FooChild().bar("Hello, Python.")
Hello, Python.
```

这样做有一些缺点，比如说如果修改了父类名称，那么在子类中会涉及多处修改，另外，Python是允许多继承的语言，如上所示的方法在多继承时就需要重复写多次，显得累赘。为了解决这些问题，Python引入了super()机制，例子代码如下：

```python
class FooParent:
  def bar(self, message):
    print(message)
class FooChild(FooParent):
  def bar(self, message):
    super(FooChild, self).bar(message)
>>> FooChild().bar("Hello, Python.")
Hello, Python.
```

表面上看 super(FooChild, self).bar(message)方法和FooParent.bar(self, message)方法的结果是一致的，实际上这两种方法的内部处理机制大大不同，当涉及多继承情况时，就会表现出明显的差异来，直接给例子：

## 代码一

```python
class A:
  def __init__(self):
    print("Enter A")
    print("Leave A")
class B(A):
  def __init__(self):
    print("Enter B")
    A.__init__(self)
    print("Leave B")
class C(A):
  def __init__(self):
    print("Enter C")
    A.__init__(self)
    print("Leave C")
class D(A):
  def __init__(self):
    print("Enter D")
    A.__init__(self)
    print("Leave D")
class E(B, C, D):
  def __init__(self):
    print("Enter E")
    B.__init__(self)
    C.__init__(self)
    D.__init__(self)
    print("Leave E")
E()
```

	结果：
	Enter E
	Enter B
	Enter A
	Leave A
	Leave B
	Enter C
	Enter A
	Leave A
	Leave C
	Enter D
	Enter A
	Leave A
	Leave D
	Leave E

执行顺序很好理解，唯一需要注意的是公共父类A被执行了多次。

## 代码二

```python
class A:
  def __init__(self):
    print("Enter A")
    print("Leave A")
class B(A):
  def __init__(self):
    print("Enter B")
    super(B, self).__init__()
    print("Leave B")
class C(A):
  def __init__(self):
    print("Enter C")
    super(C, self).__init__()
    print("Leave C")
class D(A):
  def __init__(self):
    print("Enter D")
    super(D, self).__init__()
    print("Leave D")
class E(B, C, D):
  def __init__(self):
    print("Enter E")
    super(E, self).__init__()
    print("Leave E")
E()
```

	结果：
	Enter E
	Enter B
	Enter C
	Enter D
	Enter A
	Leave A
	Leave D
	Leave C
	Leave B
	Leave E

在super机制里可以保证公共父类仅被执行一次，至于执行的顺序，是按照MRO（Method Resolution Order）：方法解析顺序 进行的。
