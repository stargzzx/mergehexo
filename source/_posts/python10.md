---
title: python 魔法方法
date: 2018-06-08 13:32:40
categories:
- python
tags:
- python
- tutorial
---
魔法方法

<!-- more -->

## 魔法方法 小例

{% codeblock %}
__init__()
	类在实例化对象的时候首先调用的方法
	不能在这个函数中给任何 return

__new__(class[,..])
	这个是在实例化之前调用的函数,第一个参数是这个类
	必须 return一个实例化的对象

	class Cap(str):
		def __new__(cls,string):
			string = string.upper()
			return str.__new__(cls,string)
	a = Cap("li cong")
	a  输出 LI CONG


__del__(self)
	自动调用，垃圾回收机制
{% endcodeblock %}

![](/images/python/10_m0.JPG)

![](/images/python/10_m1.JPG)

![](/images/python/10_m2.JPG)

![](/images/python/10_m3.JPG)

![](/images/python/10_m4.JPG)

![](/images/python/10_m5.jpg)

![](/images/python/10_m6.jpg)

## 注意事项

![](/images/python/10_mm0.png)

![](/images/python/10_mm1.png)

![](/images/python/10_mm2.png)

![](/images/python/10_mm3.JPG)
