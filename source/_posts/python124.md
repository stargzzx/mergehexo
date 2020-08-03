---
title: python | lambda 表达式
date: 2020-08-03 10:44:47
categories:
- python
- 进阶
tags:
- python
---
python 的匿名函数。

- [Python lambda](https://www.jianshu.com/p/017cc2c57f80)

<!-- more -->

## lambda介绍

`lambda`是`python`中的一个关键字，实际开发中常用`lambda表达式`的方式创建`匿名函数`，比如 `lambda a, b: a+b`，表示定义了一个匿名函数，该函数包含a、b两个参数，并返回a与b的和。lambda表达式返回的是一个函数对象，可通过函数对象来调用定义的匿名函数，比如`my_func = lambda a, b: a+b，my_func(2, 3)`则表示调用lambda定义的匿名函数；`(lambda a, b: a + b)(2, 3)`则是另一种调用方式，具体操作如下：

```python
>>> my_func = lambda a, b: a + b
>>> type(my_func)
<type 'function'>
>>> my_func
<function <lambda> at 0x0000000002E60438>
>>> my_func(2, 3)
5
>>> (lambda a, b: a + b)(2, 3)
5
```

## 剖析lambda创建匿名函数

`lambda`定义匿名函数的格式为：`lambda parameters: expression`，其中`parameters`表示定义一个或多个参数，参数类型与`def`一样，支持普通参数、默认参数、可变参数、关键字参数。`lambda`定义的匿名函数等价于如下的函数定义：

```python
def <lambda> (parametes):
    return expression
```
- `<lambda>`指函数名称 

比如：`lambda x: x + 2 `等价于 `def func_name(x): return x + 2；lambda a, b: a ** b `等价于` def func_name(a, b): return a ** b`。其中`func_name`是自定义的函数名，我们也可以通过`dis`工具分析`def`与`lambda`定义的两类函数的字节码的关系：

```python
>>> import dis
>>> def my_func(a, b, c):
        return (a + b) * c

>>> dis.dis(my_func)
  3           0 LOAD_FAST                0 (a)
              3 LOAD_FAST                1 (b)
              6 BINARY_ADD          
              7 LOAD_FAST                2 (c)
             10 BINARY_MULTIPLY     
             11 RETURN_VALUE        
>>> dis.dis(lambda a, b, c: (a + b) * c)
  2           0 LOAD_FAST                0 (a)
              3 LOAD_FAST                1 (b)
              6 BINARY_ADD          
              7 LOAD_FAST                2 (c)
             10 BINARY_MULTIPLY     
             11 RETURN_VALUE
```

可知，两个函数分别都是先对`a`、`b`变量做引用的`push stack`操作，然后执行相加，

紧接着对变量`c`做`push stack`操作，接着与`a`、`b`的和进行相乘，最后返回结果   

再次说明`lambda parameters: expression` 等价于 `def <lambda> (parametes): return expression`

## lambda特点

`lambda`是一个表达式，而不是一个语句

- lambda_expr ::= "lambda" [parameter_list]: expression

lambda是只有一行的简单表达式，并不能扩展成一个多行的代码块

Python文档说到，`lambda`不能包含语句，这是因为Python的语法框架无法处理嵌套在表达式中的语句。`lambda`可以接收多个参数，但只能有一个包含这些参数计算的表达式。这意味着`lambda`中不会含有`if... elif... else` 或者 `try... except`等语句，`lambda`的设计主要是用来计算简单的任务。

## lambda使用场景

在平时的开发过程中，使用`def`而非`lambda`定义函数足够完成各种功能的开发，但适当的使用`lambda`能够让我们的代码更加简洁明了。那在什么样的场景下推荐使用`lambda`呢？

一个小的功能函数，只需要一行就能完成，且只会被调用一次

比如要对整数列表中的所有元素添加存储单位的操作：

```python
>>> def add_unit(num):
        return '{}mb'.format(num)

>>> map(add_unit, range(5))
['0mb', '1mb', '2mb', '3mb', '4mb']

>>> map(lambda n: '{}mb'.format(n), range(5))
['0mb', '1mb', '2mb', '3mb', '4mb']
```

## 编程中与高阶函数联合使用

实际开发中，`lambda`更多的是与`map`、`filter`、`reduce`等高阶函数搭配使用，因为这些函数中需要接收函数对象作为参数，使用`lambda`达到了既定义函数又传递函数的目的，比如`map(lambda x: x ** 2, range(5))`，这里的`lambda x: x ** 2`先定义函数，然后将此函数对象作为参数传递给`map`；如果是`def`则需要首先在外部定义这个函数，而不能在`map`函数中直接通过`def`来定义，`map(def..., range(5))`，Python不允许这样的语法。

## lambda注意点

上面讲到`lambda`的特点是只能有一个表达式，使用`lambda`的目的是为了让代码更加简洁明了，所以在使用过程中需要注意以下几点：

- `lambda`专注完成简单的任务，其表达式要易读
	-不推荐在编码中为了使用`lambda`而将表达式写的很繁琐，就像`Python`文档中说到的`reduce(lambda a, b: (0, a[1] + b[1]), items)[1]`这个表达式，你能很快看出lambda函数的意图吗？答案是不能，所以对于复杂点的逻辑，最好`不要用lambda`

- 程序中多处都会用一个小函数功能，则建议用def定义这个小函数
	- 对列表中的数字求平方，`lambda`表达式为`lambda n: n ** 2`，简单，易读。如果程序中不止一处需要对数字求平方，若均使用lambda，则显得代码冗余

- 开发中先def，后lambda
	- 在实际开发过程中，我倾向于把不管什么类型的函数均先通过def来定义，等程序开发接近尾声的时候，再依据简单任务、只用一次的原则看看把哪些def定义的函数可以替换成lambda匿名函数，并写上必要的注释

## 谨慎使用lambda

其实从Python文档函数式编程的说明部分可以了解到，文档作者倾向于不使用lambda，原因就是lambda的单个表达式、无法扩展多行代码块等自身的功能限制。所以我们一定要秉持小函数、简单任务、只用一次的原则来决定是否要用lambda，千万别写出了大家半天都看不太明白的lambda表达式