---
title: python copy 模块
date: 2018-12-04 12:17:39
categories:
- python
tags:
- copy模块
- python 模块
- python
- tutorial
---
这是 copy 模块的讲解。 
<!-- more -->
[原文链接](https://blog.csdn.net/jy692405180/article/details/60881571)
## copy模块
功能描述：copy模块提供用于对对象进行克隆的api。
copy模块非常简单，只有两个api。分别是copy.copy(x)和copy.deepcopy(x)。这两个函数分别返回参数x的浅复制和深复制。
想要搞懂这两个函数的作用和区别，还是要先从“引用”说起。
## 复制引用
在Python里，一切皆对象。当用赋值运算符将某个对象x赋值给另外一个对象y时，仅仅只是将x的引用赋值给y。本质上x和y指向的是同一块内存区域。这里可以使用Python的内建函数id()进行验证：
{% codeblock %}

x = [1, 2, 3]
y = x
print(id(x) == id(y)) # True

{% endcodeblock %}
##  克隆对象
如果想要对目标进行克隆，而不是简单的引用绑定，就需要用到copy模块。使用copy模块可以创建一个与原对象一模一样的新对象。
{% codeblock %}

import copy
x = [1, 2, 3]
y = copy.copy(x)
print(id(x) == id(y)) # False

{% endcodeblock %}
## 克隆策略
当对象x里面包含了另外一个对象z，甚至对象z里面包含了对象a、对象b、对象c。情况就变得复杂了。这个时候有两种策略可以选择：

	仅仅创建一个新对象y，而y里面的对象z，通过复制引用绑定到同一个对象上。
	递归地克隆目标对象及目标对象中包含的其他对象。
	
这两种策略，就对应了copy模块里面的copy()和deepcopy()。
### 使用浅复制策略时
{% codeblock %}

import copy
x = [[1, 2, 3]]
y = copy.copy(x)
print(id(x) == id(y)) # False
print(id(x[0]) == id(y[0])) # True

{% endcodeblock %}
### 使用深复制策略时
{% codeblock %}

import copy
x = [[1, 2, 3]]
y = copy.deepcopy(x)
print(id(x) == id(y)) # False
print(id(x[0]) == id(y[0])) # False

{% endcodeblock %}
简单的说就是有没有递归调用的区别。
可以看出，深复制更加彻底，可以由里到外创建一个完完全全的新对象而不受原对象影响。但这也会导致一些问题的出现：

	对于某些过于复杂的对象，深复制可能会严重拖累程序的运行。
	某些共享数据并不需要复制，深复制会导致不同对象之间不同步。
	
当然，deepcopy()函数也使用了一些方法避免以上情况的出现：

	维护一个“memo”字典，保存当前复制操作已经复制过的对象。
	允许用户自定义克隆策略。
	
至于选择哪种克隆策略，还是要具体问题具体分析。
## 自定义克隆策略
用户自定义的类允许重写copy和deepcopy函数，这样就可以自定义调用copy模块时的操作，自行调整克隆策略：
{% codeblock %}

import copy
class a():
    # 重写copy函数无法额外参数
    def __copy__(self):
        return "override copy"

    # 重写deepcopy函数需要一个额外参数，字典类型
    def __deepcopy__(self, memo):
        return "override deepcopy"

x = a()
print(copy.copy(x)) # override copy
print(copy.deepcopy(x)) # override deepcopy

{% endcodeblock %}
## 其他需要注意的地方
copy模块无法复制诸如：type、函数、stack trace、stack frame、file、socket、window、array等类型。调用copy模块复制上述类型的对象会返回原对象或者直接报错。
字典的浅复制可以使用y = x.copy()。而列表可以通过使用切片达到同样的目的y = x[:]

