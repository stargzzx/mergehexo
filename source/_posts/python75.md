---
title: python | 类的私有变量和私有方法
date: 2020-03-14 17:07:01
categories:
- python
tags:
- python
- 私有
---
通过 「 _ 」 来规定 python 的私有变量。

<!-- more -->

<br/>

# 单下划线

<br/>

变量前的单下划线表示表面上私有
但是其实这样的实例变量外部是可以访问的，但是，按照约定俗成的规定，当你看到这样的变量时，意思就是，“虽然我可以被访问，但是，请把我视为私有变量，不要随意访问”。

<br/>

# 双下划线

<br/>

变量前的单下划线表示真正的私有,实际上的私有,只有内部可以访问，外部不能访问。

但是有窍门,可以在类里面增加get__namedede和set__namedede这样的方法来突破限制,而且还能在方法里做验证。

```python
class pub():
    _name = 'protected类型的变量'
    __info = '私有类型的变量'
    def _func(self):
        print("这是一个protected类型的方法")
    def __func2(self):
        print('这是一个私有类型的方法')
    def get(self):
        return(self.__info)
```

## 访问单下划线变量

	a = pub()
	print(a._name)

<br/>

	protected类型的变量

<br/>

## 访问双下划线变量

	a = pub()
	print(a.__info)

<br/>

	Traceback (most recent call last):
	  File "/Users/licong/python/tmp/test.py", line 13, in <module>
	    print(a.__info)
	AttributeError: 'pub' object has no attribute '__info'

<br/>

## 访问双下划线方法

	a = pub()
	print(a.__func2())

<br/>

	Traceback (most recent call last):
	  File "/Users/licong/python/tmp/test.py", line 13, in <module>
	    print(a.__func2())
	AttributeError: 'pub' object has no attribute '__func2'
