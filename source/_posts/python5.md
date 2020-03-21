---
title: python 函数详解
date: 2018-06-05 23:52:41
categories:
- python
- 基础
tags:
- python
- tutorial
---
Here is the tutorial of python.

<!-- more -->

# C

## concatenate()

numpy提供了numpy.concatenate((a1,a2,...), axis=0)函数。能够一次完成多个数组的拼接。其中a1,a2,...是数组类型的参数

```python
a=np.array([1,2,3])
b=np.array([11,22,33])
c=np.array([44,55,66])
np.concatenate((a,b,c),axis=0)  # 默认情况下，axis=0可以不写
	# array([ 1,  2,  3, 11, 22, 33, 44, 55, 66]) #对于一维数组拼接，axis的值不影响最后的结果

```
```python

a=np.array([[1,2,3],[4,5,6]])
b=np.array([[11,21,31],[7,8,9]])
np.concatenate((a,b),axis=0)
	# array([[ 1,  2,  3],
       		[ 4,  5,  6],
       		[11, 21, 31],
       		[ 7,  8,  9]])

np.concatenate((a,b),axis=1)  #axis=1表示对应行的数组进行拼接
	# array([[ 1,  2,  3, 11, 21, 31],
      		 [ 4,  5,  6,  7,  8,  9]])
```




# E


## enumerate()

enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标，一般用在 for 循环当中。

Python 2.3. 以上版本可用，2.6 添加 start 参数。

enumerate(sequence, [start=0])

```python
seasons = ['Spring', 'Summer', 'Fall', 'Winter']
list(enumerate(seasons))
	# [(0, 'Spring'), (1, 'Summer'), (2, 'Fall'), (3, 'Winter')]
list(enumerate(seasons, start=1))       # 下标从 1 开始
	# [(1, 'Spring'), (2, 'Summer'), (3, 'Fall'), (4, 'Winter')]
```

普通 for 循环

```python
i = 0
seq = ['one', 'two', 'three']
for element in seq:
    print i, seq[i]
    i +=1

	# 0 one
	# 1 two
	# 2 three
```

for 循环使用 enumerate

```python
seq = ['one', 'two', 'three']
for i, element in enumerate(seq):
    print i, element

	# 0 one
	# 1 two
	# 2 three

a = [[1,2,3],[4,5,6]]
for i,d in enumerate(a):
    print(d)
	# [1, 2, 3]
		[4, 5, 6]	
```




# F

## flatten()

首先声明两者所要实现的功能是一致的（将多维数组降位一维），两者的区别在于返回拷贝（copy）还是返回视图（view），numpy.flatten()返回一份拷贝，对拷贝所做的修改不会影响（reflects）原始矩阵，而numpy.ravel()返回的是视图（view，也颇有几分C/C++引用reference的意味），会影响（reflects）原始矩阵。

```python
x = np.array([[1, 2], [3, 4]])
x.flatten()[1] = 100
x
	# array([[1, 2],
		[3, 4]])            # flatten：返回的是拷贝
x.ravel()[1] = 100
x
	# array([[  1, 100],
		[  3,   4]])
```

## fromstring()

将字符串变为 ASCII 的代码表数组。

numpy.fromstring(string, dtype=, count=, sep='')

A new 1-D array initialized from text data in a string

因为是 ASCII 码，所以，不能转换中文，这是我自己的猜测，因为在转换中文的时候，会输出三个数字代码，看不懂。。。

```python
import numpy as np
s = "一"
a = 'yi'
print(np.fromstring(s,dtype=np.uint8))
	# [228 184 128]
print(np.fromstring(a,dtype=np.uint8))
	# [121 105]
```





# G

## global()

globals() 函数会以字典类型返回当前位置的全部全局变量




# I

## isin()

numpy.isin(element, test_elements, assume_unique=False, invert=False)

```python
import numpy as np
a = np.arange(1,10)
b = np.array([3,4,7])
print(a)
	# [1 2 3 4 5 6 7 8 9]
print(b)
	# [3 4 7]
print(np.isin(b,a))
	# [ True  True  True]
print(np.isin(b,a,invert=True))
	# [False False False]
print(np.isin(a,b))
	# [False False  True  True False False  True False False]
print(np.isin(a,b,invert=True))
	# [ True  True False False  True  True False  True  True]	
```




# J

## join()

```python
将列表，元组转为字符串
a = [1,2,3,4]
str = "".join(a)
```




# L

## len

```python
Python len() 方法返回对象（字符、列表、元组等）长度或项目个数。
语法
	len()方法语法：
	len( s )
```

## list

list() 方法用于将元组（字符串也可以）转换为列表。

注：元组与列表是非常类似的，区别在于元组的元素值不能修改，元组是放在括号中，列表是放于方括号中。

```python
print(list("123"))
	# ['1', '2', '3']
print(list(("123","234")))
	# ['123', '234']
```



# M

## min max

```python
min( x, y, z, .... )
	参数
		x -- 数值表达式。
		y -- 数值表达式。
		z -- 数值表达式。
	a = array([[1,2,3],[0,0,0],[-1,-2,-3],[2,2,2]])  
	a.min(0) #表示不同行相比较得出最小  
		array([-1, -2, -3])  
	a.min(1) #表示每一行自身元素相互比较得出最小  
		array([ 1,  0, -3,  2])  
	
	min(1,2,3)
		# 1
	
	max 和 min 一样
```




# O

## ord()

```python
返回字符在 ASCII 的位置
index = ord("a")
```




#R

## ravel()

首先声明两者所要实现的功能是一致的（将多维数组降位一维），两者的区别在于返回拷贝（copy）还是返回视图（view），numpy.flatten()返回一份拷贝，对拷贝所做的修改不会影响（reflects）原始矩阵，而numpy.ravel()返回的是视图（view，也颇有几分C/C++引用reference的意味），会影响（reflects）原始矩阵。

```python
x = np.array([[1, 2], [3, 4]])
x.flatten()[1] = 100
x
	# array([[1, 2],
		[3, 4]])            # flatten：返回的是拷贝
x.ravel()[1] = 100
x
	# array([[  1, 100],
		[  3,   4]])
```




# S


## sorted()

```python
内置函数
sorted(data, cmp=None, key=None, reverse=False)  
		#data是待排序数据，可以使List或者iterator, cmp和key都是函数，这两个函数作用与data的元素上产生一个结果，sorted方法根据这个结果来排序。 
		#cmp(e1, e2) 是带两个参数的比较函数, 返回值: 负数: e1 < e2, 0: e1 == e2, 正数: e1 > e2. 默认为 None, 即用内建的比较函数. 
		#key 是带一个参数的函数, 用来为每个元素提取比较值. 默认为 None, 即直接比较每个元素. 
		#通常, key 和 reverse 比 cmp 快很多, 因为对每个元素它们只处理一次; 而 cmp 会处理多次. 
	1. 对由tuple组成的List排序 
	students = [('john', 'A', 15), ('jane', 'B', 12), ('dave', 'B', 10),]  

	用key函数排序
	sorted(students, key=lambda student : student[2])   # sort by age  
	[('dave', 'B', 10), ('jane', 'B', 12), ('john', 'A', 15)]  
	
	用 operator 函数来加快速度, 上面排序等价于
	from operator import itemgetter, attrgetter  
	sorted(students, key=itemgetter(2))  
	
	2. 对由字典排序 
	d = {'data1':3, 'data2':1, 'data3':2, 'data4':4}  
	sorted(d.iteritems(), key=itemgetter(1), reverse=True)  
	[('data4', 4), ('data1', 3), ('data3', 2), ('data2', 1)]
	
	但是在Python3.5中：iteritems变为items
	所以上面改为
	sorted(d.items(), key=itemgetter(1), reverse=True) 
	字典是用这个函数后，会改变原来的数据结构，如：
	data = {
		1:5,2:6,3:7,4:8
	}
	print(data)
		# {1: 5, 2: 6, 3: 7, 4: 8}
	y = sorted(data.items(), key=operator.itemgetter(1), reverse=False)
	print(y)
		# [(1, 5), (2, 6), (3, 7), (4, 8)]
```

## strip()

```python
python 2.0+
str = "0000000     Runoob  0000000"; 
print str.strip( '0' );  # 去除首尾字符 0
	#     Runoob  
str2 = "   Runoob      ";   # 去除首尾空格
print str2.strip();
	#Runoob
#只移除字符串头尾指定的字符，中间部分不会移除
python3一样使用
```

## split()

```python
split(str , num)
	参数
		str 分隔符，默认为所有的空字符，包括空格、换行(\n)、制表符(\t)等。
		num 分割次数。
	str = "this is string example....wow!!!"
	print (str.split( ))
		#['this', 'is', 'string', 'example....wow!!!']
	print (str.split('i',1))
		#['th', 's is string example....wow!!!']
	print (str.split('w'))
		#['this is string example....', 'o', '!!!']
```




# Z

## zip()

zip() 函数用于将可迭代的对象作为参数，将对象中对应的元素打包成一个个元组，然后返回由这些元组组成的对象，这样做的好处是节约了不少的内存。

我们可以使用 list() 转换来输出列表。

如果各个迭代器的元素个数不一致，则返回列表长度与最短的对象相同，利用 * 号操作符，可以将元组解压为列表。

zip([iterable, ...]) iterabl -- 一个或多个迭代器;

```python
a = [1,2,3]
b = [4,5,6]
c = [4,5,6,7,8]
zipped = zip(a,b)     # 返回一个对象
zipped
	# <zip object at 0x103abc288>
list(zipped)  # list() 转换为列表
	# [(1, 4), (2, 5), (3, 6)]
list(zip(a,c))              # 元素个数与最短的列表一致
	# [(1, 4), (2, 5), (3, 6)]
 
a1, a2 = zip(*zip(a,b))          # 与 zip 相反，zip(*) 可理解为解压，返回二维矩阵式
list(a1)
	# [1, 2, 3]
list(a2)
	# [4, 5, 6]
```
