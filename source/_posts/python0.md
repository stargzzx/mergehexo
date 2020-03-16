---
title: python - numpy
date: 2018-05-20 20:39:03
categories:
- python
tags:
- python 第三方库
- python
- tutorial
- numpy
---
Here is the tutorial of python's numpy.

<!-- more -->

## 基本用法

```python
import numpy as np

# np 的矩阵都是从 0 开始的


array = np.array([[1,2,3],[4,5,6]])
print(array)
print("number of dim:",array.ndim)
print("shape:",array.shape)
print("size:",array.size)
print("type:",array.dtype) #默认int 32位

#向量和矩阵的形式区别
print(np.array([1,2,3]))              #[1 2 3]
print(np.array([1,2,3]).reshape(1,3)) #[[1 2 3]]


#类型
a = np.array([1,2,3],dtype = np.int) #默认int 32位
b = np.array([1,2,3],dtype = np.int64)
c = np.array([1,2,3],dtype = np.float) #默认为 64 位
d = np.array([1,2,3],dtype = np.float32)
print("type:",array.dtype)


a = np.zeros((3,4)) #定义为 3 行 4 列
b = np.ones((3,4))
c = np.empty((3,4)) #定义一个空矩阵 实际上很接近于零
```

## 对数组的处理

```python
a = np.arange(10,20,2)
print(a) #[10 12 14 16 18] 起始位置 步长 用来生成 数组

a = np.arange(12)
print(a) #[ 0  1  2  3  4  5  6  7  8  9 10 11]
# a = np.arange(12,4,-1) 从大到小
a = a.reshape((3,4))
print(a)
#[[ 0  1  2  3]
#[ 4  5  6  7]
#[ 8  9 10 11]]
print(a.flatten()) #将矩阵变成单个的数 [ 0  1  2  3  4  5  6  7  8  9 10 11]
#生成线段
a = np.linspace(1,10,4)
print(a) #[ 1.  4.  7. 10.] 从 1 到 10 生成 4 段的数列
```

## 基本属性

	a.ndim   #数组的维数
	a.shape  #数组每一维的大小
	a.size   #数组全部元素的数量 
	a.dtype  #数组中元素的类型
	a.shape  #返回具体的维度
	print a.itemsize  #每个元素所占的字节数
	
```python
b = np.arange(1,10)
print(b.dtype)
	# int32
```

## numpy 的基本运算

```python
a = np.array([10,9,8])
b = np.arange(3)

矩阵之间的加减运算
c = a - b
print(c) #[10  8  6]

data = np.array([[1,2],[2,2]])
print(1 - data);
	[[ 0 -1]
	[-1 -1]]
print([1,2] - data);
	[[ 0  0]
	[-1  0]]
np中的矩阵加减并不需要是同规格矩阵

c = b ** 2 #python 中次方，平方都是 **
print(c) #[0 1 4]

d = np.sin(10)
print(d) #-0.5440211108893698
c = 10 * np.sin(a)
print(c) #[-5.44021111  4.12118485  9.89358247]

print(b < 2) #[ True  True False]

#矩阵点乘
c = a * b
print(c) #[ 0  9 16]
#矩阵相乘
c = np.dot(a,b)
print(c) #25

a = np.array([[1,2,3],[3,4,5]])
b = np.array([[1,0],[0,1],[1,1]])
c = np.dot(a,b)
print(c) #a = 2 * 3  b = 3 * 2
#[[4 5]
#[8 9]]

#也可以这样
c = a.dot(b)
print(c) #和上面一样的结果

a = np.random.random((2,4))
print(a) #随机生成范围 0 - 1 的 2 * 4 矩阵

a = np.array([2,3,4])
print(np.sum(a))
print(np.min(a))
print(np.max(a))

a = np.array([[1,2,3],[3,4,5]])
print(np.sum(a,axis=1)) #[ 6 12]  当 axis 为 1 的时候是行相加
print(np.sum(a,axis=0)) #[4 6 8]  为列相加

print(np.max(a,axis=1)) #[3 5]

a = np.array([[1,2,3],[1,1,4]])
print(np.argmin(a)) #0 最小值的索引 只输出第一个
print(np.argmax(a)) #5 最大值的索引
print(np.mean(a))   #2.0 平均値
print(a.mean())     #和上面一样
print(np.average(a))#2.0 可以计算加权平均数
print(np.cumsum(a)) #[ 1  3  6  7  8 12] 一个个的累加
print(np.diff(a))   #前一个元素减后一个元素 每个行独立
#[[1 1]
#[0 3]]
print(np.nonzero(a)) #(array([0, 0, 0, 1, 1, 1], dtype=int64), array([0, 1, 2, 0, 1, 2], dtype=int64))
a = np.array([[1,2,3],[0,1,4]])
print(np.nonzero(a)) #(array([0, 0, 0, 1, 1], dtype=int64), array([0, 1, 2, 1, 2], dtype=int64)) 输出非零的索引
a = np.array([[1,2,3],[5,1,4]])
print(np.sort(a))

#[[1 2 3]
#[1 4 5]]
print(np.transpose(a)) #矩阵的转置
#[[1 5]
# [2 1]
# [3 4]]
print(a.T) #和上面一样

a.I	#求逆操作，这个求逆的操作是对矩阵管用，关于矩阵和数组在 numpy 的区别可以参考我的博文。

print(np.clip(a,5,9)) #在矩阵中凡是小于 5 的都等于 5 凡是 大于 9 的都等于 9
#[[5 5 5]
# [5 5 5]]
a = np.array([1,1,1])
print(a.T) #对于 单行矩阵是没办法将其变为向量的
print(a.reshape((1,3))) #可以这样
```

## 通过索引找元素

```python
a = np.arange(3,15).reshape((3,4))
print(a[2]) #[11 12 13 14]
print(a[2,1])  #12 索引具体数
print(a[2,:])  #[11 12 13 14] : 代表所有
print(a[:,1])  #[ 4  8 12]    打印某一列
print(a[1,1:3]) #[8 9] 第 1 行中的第 1 ， 2 列
for row in a:
    print(row) #输出每一行
#[ 7  8  9 10]
#[11 12 13 14]
for column in a.T:
    print(column) #输出每一列
#[ 3  7 11]
#[ 4  8 12]
#[ 5  9 13]
#[ 6 10 14]
for item in a.flat: #和a.flatten() 不同 a.flat 返回的是迭代器
    print(item) #输出每一个元素
```

## 合并

```python
a = np.array([1,1,1])
b = np.array([2,2,2])
print(np.vstack((a,b))) # 向下合并
#[[1 1 1]
# [2 2 2]]
print(np.hstack((a,b))) # 横向合并
#[1 1 1 2 2 2]
print(np.vstack((a,a,b))) #多个合并
```

## 分割

numpy 的分割和 python 自带的切片不一样。

```python
a = np.arange(12).reshape((3,4))
print(np.split(a,2,axis=1)) #将矩阵 a 按照行分割成 2 个片段
#[array([[0, 1],
#       [4, 5],
#       [8, 9]]), array([[ 2,  3],
#       [ 6,  7],
#       [10, 11]])]
print(np.split(a,3,axis=0)) #[array([[0, 1, 2, 3]]), array([[4, 5, 6, 7]]), array([[ 8,  9, 10, 11]])]
#进行不等量分割
print(np.array_split(a,3,axis=1))
#[array([[0, 1],
#       [4, 5],
#       [8, 9]]), array([[ 2],
#       [ 6],
#       [10]]), array([[ 3],
#       [ 7],
#       [11]])]
print(np.vsplit(a,3))#[array([[0, 1, 2, 3]]), array([[4, 5, 6, 7]]), array([[ 8,  9, 10, 11]])]
print(np.hsplit(a,2))
#[array([[0, 1],
#       [4, 5],
#       [8, 9]]), array([[ 2,  3],
#       [ 6,  7],
#       [10, 11]])]


# copy and deep copy
a = np.array([1,1,1])
b = a.copy()
a[0] = 2
print(a) #[2 1 1]
print(b) #[1 1 1]
```

## 小技巧

### 1

在 python 中下面的句子是不成立的。

```python
a = [0 , 1]
b = [True,False]
print(a[b])
```

但是在 numpy 中这种语句是成立的，比如

```python
import numpy as np
points = np.arange(1,11,step=1)
cross_points = np.random.randint(0, 2, size=10).astype(np.bool)
print(points)
	# [ 1  2  3  4  5  6  7  8  9 10]
print(cross_points)
	# [ True  True False False  True  True  True  True False False]
print(points[cross_points])
	# [1 2 5 6 7 8]
```

在 numpy 中，我们可以看到，第一它们的数据类型并不是列表，也就是没有逗号，第二，他们都是 numpy 的数据类型。

这一点技巧很重要，因为它可以直接换取局部数值。

```python
import numpy as np
points = np.arange(1,11,step=1)
cross_points = np.random.randint(0, 2, size=10).astype(np.bool)
random_i = np.random.randint(0,len(points),size=len(points))
print(points)
	# [ 1  2  3  4  5  6  7  8  9 10]
print(cross_points)
	# [False False  True  True  True False False False  True  True]
print(points[cross_points])
	# [ 3  4  5  9 10]
print(random_i)
	# [5 2 4 7 1 6 7 0 8 2]
points[cross_points] = random_i[cross_points]
print(points)
	# [1 2 4 7 1 6 7 8 8 2]	
```

上面的小技巧交给我们如何直接局部值。非常重要，装逼必备。

### 2

我们知道用 numpy 创造的数据是矩阵，而矩阵的特性是和 python 的那些数据类型不一样的，比如，它可以直接比较，如下面的装逼必备代码。

```python
import numpy as np
a = np.arange(1,10)
b = a.copy()
b[0] = 9
b[4] = 11
print(a == b)
	# [False  True  True  True False  True  True  True  True]
print((a == b).sum(axis=0))
	# 7
```

### 3 取反

```python
import numpy as np
a = np.arange(1,11)
b = np.random.randint(0,2,size=10).astype(np.bool)
print(b)
	# [False False False False False  True False  True False False]
print(~b)
	# [ True  True  True  True  True False  True False  True  True]
```

### 4 直接改变 list 元素

```python
v = np.array([1,2,3,4])
v[v > 2] = 3
print(v)
	# [1,2,3,3]
```

## numpy 支持的数据类型

	bool_		以字节存储的布尔值（True 或 False）
	int_		默认的整数类型（和 C 的 long 一样，是 int64 或者 int32）
	intc		和 C 的 int 相同（一般为 int64 或 int32）
	intp		用于下标的整数（和 C 的 ssize_t 相同，一般为int64 或者 int32）
	int8		字节（-128 到 127）
	int16		整数（-32768 到 32767）
	int32		整数（-2147483648 到 2147483647）
	int64		整数（-9223372036854775808 到 9223372036854775807）
	uint8		无符号整数（0 到 255）
	uint16		无符号整数（0 到 65535）
	uint32		无符号整数（0 到 4294967295）
	uint64		无符号整数（0 到 18446744073709551615）
	float_		float64 的简写
	float16		半精度浮点：1位符号，5位指数，10位尾数
	float32		单精度浮点：1位符号，8位指数，23位尾数
	float64		双精度浮点：1位符号，11位指数，52位尾数
	complex_	complex128 的简写
	complex64	由两个32位浮点（实部和虚部）组成的复数
	complex128	由两个64位浮点（实部和虚部）组成的复数
	
## 术语解析

### array_like 和 iterable

术语“array-like”确实仅在NumPy中使用，并且是指可以作为第一个参数传递给numpy.array()的任何东西来创建数组。

术语“iterable”是标准的python术语，是指可以迭代的任何东西(例如，在可迭代中使用x)。

大多数类似数组的对象是可迭代的，除了标量类型。

许多迭代不是数组的 – 例如，您不能使用numpy.array()从生成器表达式构造NumPy数组。 (您将不得不使用numpy.fromiter()，但是在NumPy文档的术语中，生成器表达式不是“类似数组”)。

## 在项目中 numpy 究竟是如何处理数据的

numpy 的数据类型是向量和矩阵。

所以，处理起来要转变思维，因为它有时候并不是一个单独的向量，而是很多向量聚在一起的矩阵，相当于处理多维数组。

但是，相较于 python 处理多维数组，在 numpy 中处理矩阵的代码就好像是在处理一维数组一样。

思维，一定要转变。

![](/images/python/0.jpg)

