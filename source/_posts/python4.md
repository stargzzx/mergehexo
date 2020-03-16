---
title: python详解
date: 2018-06-03 16:28:51
categories:
- python
tags:
- python
- tutorial
---
Here is the tutorial of python.

<!-- more -->

# 小技巧

```python
获得关于类型的信息
	有type() 但推荐用 isinstance()
	isinstance()两个参数，第一个为要判断的数据，第二个为你猜的数据类型，返回一个布尔类型
		isinstance(a,str)

python3 用了真正的除法
	10/8 = 1.25
	如果是用双斜杠是地板除法(原来除法)
		10//8 = 1
		3.0//2 = 1.0

python的幂
	3**2 = 9

在python中没有else if 只有elif

在python中三元操作符是这样的
	small = x if x<y else y
		就相当于
		x,y = 4,5
		if x<y:
			small = x
		else:
			small = y

断言
	assert

在python中，字符也就是数组
	str = "123"
	for i in str:
		print(i,end = ' ')
	打印结果为：
	 	1 2 3

range()有三个参数，可查doc

for倒序输出
	for x in range(9,-1,-1):
	
双重 for 
	a = [1,2,3]
	b = [4,5]
	c = [a[x] + b[y] for x in range(3) for y in range(2)]
	print(c)
		# [5, 6, 6, 7, 7, 8]
		# 解析，前面的那个在最外层
	
```

# 安装 whl

```python

whl格式本质上是一个压缩包，里面包含了py文件，以及经过编译的pyd文件。使得可以在不具备编译环境的情况下，选择合适自己的python环境进行安装。

安装方法很简单，进入命令行输入

pip install xxxx.whl

pip install C:\Users\xxx\Downloads\python_dateutil-2.5.3-py2.py3-none-any.whl

```

# 编码

```python
python2 中
	s = "我爱你"
	windows 下中文默认为GB2312保存
	linux 下中文默认为utf-8保存
	s = u"我爱你"
		使中文用unicode编码

decode 解码
	s = "我爱你"
	s.encode("utf-8")
		会报错，因为用encode的前提是原字符是Unicode编码
	s.decode("GB2321") #指明原来编码格式，将其转化为Unicode编码
	合在一起
	s.decode("unicode").encode("utf-8")

python3
	中文默认用unicode	
```

# 控制讲解

## break

break 只是跳出离它最近的循环

```python
for i in range(2):
    for j in range(2):
		if(j == 1):
			break
        print(i,j)
		# 0 0
			1 0		
```

# 数据类型

## 序列

```python
序列包括列表，元组，字符串

举个例子拿列表的sort和下面全局函数的sorted举例
	a = (1,3,5,6,2,4)
	a.sort() 这是内置函数的用法
	sorted(a) 这是全局函数的用法

tuple() 把可迭代的对象转为元组

str() 把可迭代的对象转为字符串	

list() 把一个可迭代对象转化为列表
	b = "li cong"
	b = list(b)
	b : ["l","i","c","o","n","g"]
	c = (1,1,2,3,5)
	c = list(c)
	c ： ["1","1,"2","3","5"]

max() 返回序列中的最大值(即便是字符串也可以返回，序列内部元素类型必须相同)

min()

sum() 必须是数字类型，字符串不行

sorted()

reversed()

enumerate()	返回一个元组，元组中是由下标和数值组成
	a = (1,2,a)
	list(enumerate(a))
		[(0,1),(1,2),(2,a)]
		
a =[1,2,3,7,8,9]
b = [4,5,6]
list(zip(a,b))  多余的舍弃  zip 将两数以元组的形式绑在一起
	[(1,4),(2,5),(3,6)]

```

## 列表

```python
创建固定长度的列表
tmp = [0 for k in range(0,9)]

数组中可以添加不同数据类型的元素
向数组中添加有append()方法
	但是这种方法只能支持一个独立参数
	要是添加多个可以用extend（）将参数作为一个列表去扩展列表末尾
	extend()方法上传多个需要事先变成组
		a = [2]
		extend(["123","我爱你"]) 对
		a = [2,"123","我爱你"]
		extend("123","我爱你")   错
		a = [1]
		a.append([1,2])
		a = [1,[1,2]]
		
		a = [1,2,3]
		b = [4,5,6]
		a.append(b)
		a	#[1,2,3,[4,5,6]]
		
		a = [1,2,3]
		b = [4,5,6]
		a.extend(b)
		a	#[1,2,3,4,5,6]
	还有insert()方法添加元素

删除数组元素
	remove() 必须制定内含元素
	del 这是一个语句，不是内置函数，用法
		mem = [1,2,3]
		del mem[1]
			mem = [1,3]
		del mem 删除整个列表
	
	pop() 默认删除最后一个元素，并返回
		可以加上参数，指定位置

列表分片
对于具有序列结构的数据来说，切片操作的方法是：consequence[start_index: end_index: step]。
start_index：表示是第一个元素对象，正索引位置默认为0；负索引位置默认为 -len(consequence)
end_index：表示是最后一个元素对象，正索引位置默认为 len(consequence)－1；负索引位置默认为 -1。
step：表示取值的步长，默认为1，步长值不能为0。
[注意]对于序列结构数据来说，索引和步长都具有正负两个值，分别表示左右两个方向取值。索引的正方向从左往右取值，起始位置为0；负方向从右往左取值，起始位置为-1。因此任意一个序列结构数据的索引范围为 -len(consequence) 到 len(consequence)-1 范围内的连续整数。
	
	mem = [1,2,3,4]
	mem[1:3] 输出2,3 
	mem[:]获得一个列表的拷贝
		mem1 = mem[:] 是完全拷贝，而不是地址指向，改变任意一个都不会影响另一个
copy() 的最后和 [:] 结果一样

列表可以直接拼接
	mem + mem1

in的用法

	123 in mem 如果存在会返回true
	123 not in mem
	mem = [1,2,["li"],3]
	li in mem 返回false
	li in mem[2] 返回true

列表也可以用乘法
	 mem = [1,2]
	mem*=2
		mem = [1,2,1,2]
	
count（）的用法
	mem = [1,1,2,3,1,4]
	mem.count(1) 是3

index()返回参数在列表中的位置
	有三个参数	第一个为元素，二三为范围

reverse()
	将列表进行翻转

sort()
	三个参数，第一个为指定算法，第二个不说，第三个为reverse如果指定为true，将翻转拍，从大到小
	sort(reverse = true)
	
列表推导式又叫列表解析，用来动态创建列表

形式：
	[有关A的表达式 for A in B]  能写成很复杂的式子
	[x*x for x in range(3)]
		[0,1,4,9]
		
	dataSet = [
        [1,1,'yes'],
        [1,1,'yes'],
        [1,0,'no'],
        [0,1,'no'],
        [0,1,'no']
    ]
	for i in range(len(dataSet[0]) - 1):
		data = [example[i] for example in dataSet]
		print(data)
	
	#	[1, 1, 1, 0, 0]
	#	[1, 1, 0, 1, 1]
```

## 字典

```python
字典是映射类型 键值对 为无序

对字典的基本操作
	data = {'A':{}}
	data['A'][0] = 1
	print(data)
		# {'A': {0: 1}}
		
	data = {}
	data['A'] = 1
	print(data)
		# {'A': 1}
		
	data = {}
	data['A'] = 1
	data['A'][0] = 2
	print(data)
		# TypeError: 'int' object does not support item assignment
	不能越级进行添加
	
	data = {}
	data['A'] = 1
	data['A'][1] = 2
	print(data)
		# 错误
	因为在执行完 data['A'] = 1 后，data 为 {'A':1}，下面的那个属于越级处理
	
	data = {}
	data['A'] = {}
	data['A'][1] = 2
	print(data)
		# {'A': {1: 2}}


创建
	a = {"a" : "这是一个a" , "b" : "这是一个b"}
	
dict() 用函数来创建字典，有很多形式
	dict1 = dict((("a":1),("2":a),("4",b))) 注意括号的个数，另外里面传递列表也可以
		输出为{"a":1,"2":a,"4",b}
	dict2 = dict(小鲫鱼 = "1",李丛 = "0")
		dict2 = dict("小鲫鱼" = "1",李丛 = "0")	这是错误的，不能将key用引号括起来，默认就是字符串
	可以直接通过key改变数值
		dict["a"] = b,要是没有此key则会自动添加
		
fromkeys()
	对字典进行批量化初始化，记住是初始化，要是对现存字典调用这个，会创建新的字典，覆盖原来的
	dic = {}
	dic.fromkeys((1,2,3),'number')
		所以dic为{1:"number",2:"number",3:"number"}
		
keys()
	返回字典所有的键，没有参数
	
values()
	返回字典所有的值，没有参数
	
items()
	返回字典所有的键值对，没有参数
	
get()
	利用键访问值
	get(12),假如没有此键会返回None
	get(12,"没有")
		假如key所对应的12有值，则返回这个值
		否则返回没有
		
	get的用法
	get(self,key,default=None)
		参数
			key -- 字典中要查找的键。
			default -- 如果指定键的值不存在时，返回该默认值值
		返回 
			返回指定键的值，如果值不在字典中返回默认值None
		实例
			data = {
				'A' : 0,
				'B' : 6
			}
			data['B'] = data.get('B',2) + 1
			print(data)
				#{'A': 0, 'B': 7} 虽然在get B 时后面跟着2，但是由于B原本是存在的，所以get('B',2)实际上返回的就是B的值，上面的那句话，和data['B'] = data.get('B',3) + 1没有区别
			
			data = {
				'A' : 0,
				'B' : 6
			}
			data['C'] = data.get('C',2) + 1
			print(data)
				#{'A': 0, 'B': 6, 'C': 3}
				由于 C 在字典中是不存在的，所以单纯的get('C') 时会返回None，但是get一个不存在的数，并且后面跟着数据，意思是初始化这个值为你所给的数据（数据包括字符型或者数字型）
		
		获得字典中的数值，也可以像是数组一样操作
		data = {
			'A' : 0,
			'B' : 6
		}
		data[0][0]
			# 0
clear()
	清空
	
copy()
	为浅拷贝，不是赋值，地址不一样
	
pop(key)
	会弹出相应的键值对，弹出后，原来在里面的就没有了
	
popitem()
	随机弹出一个，无参数
	
setdefault(key)
	和get方法差不多，但如果找不到，会添加，添加的值为None
	也可以给值 setdefault(1,"licong")
	
update()
	利用一个字典去更新另一个字典
	a = {"1":"q"}
	b = {"1":"a","2":"b"}
	b.update(a)
		b = {"1":"q","2":"b"}
		
改变字典中的数值
data = {
    'A' : 0,
    'B' : 6
}
data['A'] = data['A'] + 7;
print(data.get('A'))
	#7
```

## 集合

```python
集合 set
	a = {} a为字典
	a = {1,2,3} a为集合，因为没有体现映射关系

集合里面的值是唯一的
集合是无序的，不支持索引

set() 创建集合，工厂函数，可以通过接受其他可迭代对象，进行删除重复元素

内置方法

	add()
	
	remove()

不可变集合，不能添加和删除
	a = frozenset([1,2,3,4]) 创建不可变集合
```

## 元组

```python
元组是不可改变的，不能删除，插入

创建元组，用小括号
	a = ()
	a = (1,2,3)
	a[1]    输出2
	如果只是创建一个元素的元组必须加逗号
		a = (1,)
			否则a会被认为是int
			
元组的切片和列表一样,如
	b = a[:]

元组是不能添加元素的，如果非要添加，只能曲线救国，用切片
	a = ("呵呵"，"哈哈")
	a = a[:1] + ("咯咯"，) + a[1:]
		a 为 = ("呵呵"，"咯咯","哈哈")

del a 删除整个元组

```

# 面向对象

```python
class 中的函数，默认第一个都会传递self
class a:
	def a(self,name):
		self.name = name
b = a()
a.a("李丛")  传递的参数是不关self的事的，即便是把self写在形参里面

继承
	class a(list):
		code
	子类重写父类的方法会把父类的方法给覆盖掉
	要想不覆盖，需
	class a:
		def b():
			code

方法一：class c(a):
		def b():
			a.b(self) 直接调用父类的方法，这里的self是子类的实例对象
			code

方法二：class c(a):
		def b():
			super().b() 这个更好
			code 

python支持多个继承
	class a(b,c,v...):
		pass

魔法函数：
	__init__(slef)
		这是初始化函数，用于创建时候调用
	class a:
		def __init__(self,name):
			self.name = name
	b = a("李丛")

公有和私有
	在python中定义私有变量只需要在变量名或函数名前加上两个 _ 即可

静态方法
	在方法面前加上@staticmethod 修饰符
	静态方法不需要绑定到对象上，可直接引用
	静态方法也没有self
	静态方法在内存中只会生成一个

静态属性
	没有加self的属性都是静态属性

类，类对象，实例对象
	calss A:
		name = "1"
		def b():
			code
		A.name
			输出1
		A.b()
			报错
			即便A不实例化对象，依然可以访问属性，但是不能访问方法
组合

相关方法

issubclass()
	判断前一个类（不是实例）是否是第二个参数的类的子类
		宽松检验
			一个类可以是自己的子类

isinstance()
	判断前一个实例对象是否是第二个参数类的实例对象

hasattr(object,name)
	看对象（实例对象）是否有指定属性，name传递的是字符串

getattr(object,name[,default])
	getattr(a,"name","你所访问的不存在")

setattr(object,name,value)
	
```

# 函数
## 函数 函数介绍
```python
####
```

## 函数 参数

```python
用 def 来创建

def function():
	pass

函数中有两种参数，一为形式参数，二为实际参数

	形参是指小括号里面的参数
		def function(name):	
			pass   其中name就是形参
	实参是指传递进来的参数，为具体数据

可以在定义函数的时候写函数文档

	def function(name):
		'需要传递一个参数，是name'
		print(name)
		在执行时，函数文档，也就是单引号中的内容是不会打印的
	若是想查看文档，直接funtion.__doc__ 文档直接作为函数的一个属性

关键字参数，因为在传递形参时可能会搞乱顺序

	def function(a,b):
		print(a + " " +b)
	function(b = 1,a = 7)
		输出 7 1

默认参数

	def function(name = "licong"):
		print(name)
	function()
		输出 licong
	function(123)
		输出123	

收集参数(可变参数，不确定有多少参数)

	def function(*name):  （会用元组打包起来）
		print(len(name))
	function(1,2,3)
		输出 3		
```

## 函数 过程

```python
python 只有函数没有过程，是因为每个函数都有返回值，即便是没有 return，返回的是none
python返回多个值，可以用列表
如果函数试图在函数内修改全局变量，那么python会自动创建一个相同名字的局部变量代替
	a = 0	
	def function():
		a =1
	print(a)
		function()
			最终输出0
```

## 函数 lambda表达式

```python
lambda表达式也就是匿名函数
def ds(x):
	return 2 *x

相当于 lambda x: 2*x
	:前面是要传的参数 后面是返回值

例子：

	g = lambda x: 2*x
	g(5)
		输出 10
	多个参数
		g = lambda x,y: 2*x*y

	def is_odd(x):
		if x % 2:
			return x
		else:
			return None
	相当于
		lambda x : x if x % 2 else None
```

## 将函数作为参数

```python
def a():
    print("1")

def b(callback):
    callback()

b(a)
	输出 1

def a():
    return 1

def b(callback):
    a = callback
    print(a)

b(a())
	输出 1

当传递的参数为function不带括号时，传的是一个方法

当传递的参数为function()带括号时，传的是一个方法返回值

```

## 函数 内嵌函数和闭包

```python
修改全局变量 
	运用 global 关键字
	a = 10
	def function():
		global a
		a = 5
	print(a)
		function()
		输出5

内嵌函数
	函数内部再创建函数

	def fun1():
		print(1)
		def fun2():
			print(2)	
		fun2()

	fun1()
		输出 1 2

闭包 在一个内部函数里，对外部作用域的参数进行引用
	def fun1(a):
		def fun2(b):
			return a * b	
		return fun2		fun2为一个内部函数，对外部作用域的a进行引用，为闭包
	
	i = fun1(8)
	type(i) 	输出function
	i(5)	输出40
	也可以这么写 fun1(8)(5) 输出40

	
	例子：
		def fun1():
			a = 1
			def fun2()
				print(a)
				a = 1
			fun2()

			fun2() 执行起来会报错，因为内部函数和外部函数有一个变量重名，所以python会将内部函数屏蔽起来

闭包的陷阱
	def fun1():
		x = 5
		def fun2():
			x*=x
			return x	
		return fun2()

	fun1()
		会报错 因为最后return的一个方法的执行，对于fun2()来说，编译器会将其看做一个独立的函数，而不是内置函数，会屏蔽掉外部的x，所以x是没有被定义的

	解决方案：

	1.	python3之前的解决方案
		def fun1():
		x = [5]
		def fun2():
			x[0]*=x[0]
			return x	
		return fun2()
		
		fun1()	
			输出25，因为数组会存储在栈中，为一个单独的容器，可以进行修改
	
	2.	python3的解决方案
		nonlocal进行声明

		python3之前的解决方案

		def fun1():
			x = 5
			def fun2():
				nonlocal x   嵌套函数中，内部函数修改外部函数的局部变量
				x*=x
				return x
			return fun2()
		fun1() 输出25

```

![](/images/python/4_fun.jpg)

# 其他

## 迭代

```python
iter()
	对容器调用这个方法生成一个迭代器
	string  = "licong"
	a = iter(string)

next()
	用next调用迭代器，出现下一个元素
	next(a)	
		输出 l
	没有元素会报出异常

魔法方法
__iter__()
__next__()
	决定了迭代器的规则
```

## 描述符

```python
将某种特殊的类的实例指派给另一个类的属性
class a():
	pass

class b():
	p = a()
	其中p为描述符

class A:

    def __init__(self,val = 10):
        self.val = val

    def __set__(self, instance, value):
        self.val = value
        print("属性被设置")

    def __get__(self, instance, owner):
        print("属性正在被访问")
        return self.val

class B:
    a = A(12)

b = B()
b.a = 10
	输出	属性被设置

b.a
	输出 属性正在被访问
```

## 扩展else语句和with语句

```python
while 和 else 语句配合 不满足while则执行else

	while 条件：
		code
		break
	else：
		code

try 和 else 语句配合

	try：
	except:
	else:
		如果try出现异常，else的语句不会被打印
		如果try的语句执行正常，else语句会被执行

with语句

	打开文件后自动关闭文件
	try:
		with open("path","r") as f:
			for esch in f:
				print(each)
	except
```

 
 