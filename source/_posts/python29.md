---
title: python Math 模块
date: 2018-06-08 12:18:36
categories:
- python
- 模块
- math
tags:
- math模块
- python 模块
- python
- tutorial
---
python 中 math 模块。

<!-- more -->

## math

[转载](http://www.cnblogs.com/vamei)

```python
math包主要处理数学相关的运算。math包定义了两个常数:
math.e   # 自然常数e
math.pi  # 圆周率pi
math.inf # 无穷大

此外，math包还有各种运算函数 (下面函数的功能可以参考数学手册)：
math.ceil(x)       
	# 对x向上取整，比如x=1.2，返回2
math.floor(x)      
	# 对x向下取整，比如x=1.2，返回1
math.pow(x,y)      
	# 指数运算，得到x的y次方
math.log(x)        
	# 对数，默认基底为e。可以使用base参数，来改变对数的基地。比如math.log(100,base=10)
	#有的时候 math.log(100,base = 10) 出错，但是 math.log(100,10) 就对
math.sqrt(x)       
	# 平方根

三角函数: math.sin(x), math.cos(x), math.tan(x), math.asin(x), math.acos(x), math.atan(x)

这些函数都接收一个弧度(radian)为单位的x作为参数。

角度和弧度互换: math.degrees(x), math.radians(x)

双曲函数: math.sinh(x), math.cosh(x), math.tanh(x), math.asinh(x), math.acosh(x), math.atanh(x)

特殊函数： math.erf(x), math.gamma(x)
```
