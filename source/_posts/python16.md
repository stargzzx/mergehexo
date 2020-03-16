---
title: 论numpy中matrix 和 array的区别
date: 2018-06-15 09:39:30
categories:
- python
tags:
- python 第三方库
- python
- tutorial
- numpy
---
[转载](https://blog.csdn.net/vincentlipan/article/details/20717163)

Numpy matrices必须是2维的,但是 numpy arrays (ndarrays) 可以是多维的（1D，2D，3D····ND）. Matrix是Array的一个小的分支，包含于Array。所以matrix 拥有array的所有特性。
<!-- more -->

在numpy中matrix的主要优势是：相对简单的乘法运算符号。例如，a和b是两个matrices，那么a*b，就是矩阵积。

```python
import numpy as np

a=np.mat('4 3; 2 1')
b=np.mat('1 2; 3 4')
print(a)
	# [[4 3]
	#  [2 1]]
print(b)
	# [[1 2]
	#  [3 4]]
print(a*b)
	# [[13 20]
	#  [ 5  8]]
```

matrix 和 array 都可以通过objects后面加.T 得到其转置。但是 matrix objects 还可以在后面加 .H f得到共轭矩阵, 加 .I 得到逆矩阵。

相反的是在numpy里面arrays遵从逐个元素的运算，所以array：c 和d的c*d运算相当于matlab里面的c.*d运算。

```python
c=np.array([[4, 3], [2, 1]])
d=np.array([[1, 2], [3, 4]])
print(c*d)
# [[4 6]
#  [6 4]]
```

而矩阵相乘，则需要numpy里面的dot命令 :

```python
print(np.dot(c,d))
	# [[13 20]
	#  [ 5  8]]
```

** 运算符的作用也不一样 ：

```python
# 对于 矩阵的 ** 相当于两个矩阵相乘
print(a**2)
	# [[22 15]
	#  [10  7]]

# 对于 数组的 ** 相当于单独的每一个元素的平方
print(c**2)
	# [[16  9]
	#  [ 4  1]]
```

问题就出来了，如果一个程序里面既有matrix 又有array，会让人脑袋大。但是如果只用array，你不仅可以实现matrix所有的功能，还减少了编程和阅读的麻烦。

当然你可以通过下面的两条命令轻松的实现两者之间的转换：np.asmatrix和np.asarray

对我来说，numpy 中的array与numpy中的matrix，matlab中的matrix的最大的不同是，在做归约运算时，array的维数会发生变化，但matrix总是保持为2维。例如下面求平均值的运算。

```python
m = np.mat([[1,2],[2,3]])
m
	# matrix([[1, 2],
	#         [2, 3]])
mm = m.mean(1)
mm
	# matrix([[ 1.5],
	#   	[ 2.5]])
mm.shape
	# (2, 1)
m - mm
	# matrix([[-0.5,  0.5],
    #     [-0.5,  0.5]])	
```

对array 来说

```python
a = np.array([[1,2],[2,3]])
a
	# array([[1, 2],
	#        [2, 3]])
am = a.mean(1)
am.shape
	# (2,)
am
	#array([ 1.5,  2.5])
a - am #wrong
	# array([[-0.5, -0.5],
	#        [ 0.5,  0.5]])
a - am[:, np.newaxis]  #right
	# array([[-0.5,  0.5],
	#        [-0.5,  0.5]])
```

我在其他地方找到的总结：

[转载](http://blog.sina.com.cn/s/blog_5d8dae7c0100zms5.html)

Matrix类型继承于ndarray类型，因此含有ndarray的所有数据属性和方法。Matrix类型与ndarray类型有六个重要的不同点，当你当Matrix对象当arrays操作时，这些不同点会导致非预期的结果。

	1）Matrix对象可以使用一个Matlab风格的字符串来创建，也就是一个以空格分隔列，以分号分隔行的字符串。
	2）Matrix对象总是二维的。这包含有深远的影响，比如m.ravel()的返回值是二维的，成员选择的返回值也是二维的，因此序列的行为与array会有本质的不同。
	3）Matrix类型的乘法覆盖了array的乘法，使用的是矩阵的乘法运算。当你接收矩阵的返回值的时候，确保你已经理解这些函数的含义。特别地，事实上函数asanyarray(m)会返回一个matrix，如果m是一个matrix。
	4）Matrix类型的幂运算也覆盖了之前的幂运算，使用矩阵的幂。根据这个事实，再提醒一下，如果使用一个矩阵的幂作为参数调用asanarray(...)跟上面的相同。
	5）矩阵默认的__array_priority__是10.0，因而ndarray和matrix对象混合的运算总是返回矩阵。
	6）矩阵有几个特有的属性使得计算更加容易，这些属性有：
		(a) .T －－ 返回自身的转置
		(b) .H －－ 返回自身的共轭转置
		(c) .I －－ 返回自身的逆矩阵
		(d) .A －－ 返回自身数据的2维数组的一个视图（没有做任何的拷贝）

Matrix类 是ndarray的一个Python子类，你也可以学习这个实现来构造自己的ndarray子类。Matrix对象也可以使用其它的Matrix对象，字 符串，或者其它的可以转换为一个ndarray的参数来构造。另外，在NumPy里，“mat”是“matrix”的一个别名。