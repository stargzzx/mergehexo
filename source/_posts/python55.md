---
title: python 的 glob
date: 2019-02-25 10:47:03
categories:
- python
tags:
- python
- python 模块
- python模块
- glob
---
这是 python 的 glob 模块的教程。

<!-- more -->

## 参考资料

[python glob模块](https://blog.csdn.net/csapr1987/article/details/7469769)

## 介绍

glob模块是最简单的模块之一，内容非常少。用它可以查找符合特定规则的文件路径名。跟使用windows下的文件搜索差不多。查找文件只用到三个匹配符："*", "?", "[]"。"*"匹配0个或多个字符；"?"匹配单个字符；"[]"匹配指定范围内的字符，如：[0-9]匹配数字。

## 函数列表

### glob

返回所有匹配的文件路径列表。它只有一个参数pathname，定义了文件路径匹配规则，这里可以是绝对路径，也可以是相对路径。下面是使用glob.glob的例子：

	import glob    
	#获取指定目录下的所有图片  
	print glob.glob(r"E:\Picture\*\*.jpg")  
	#获取上级目录的所有.py文件  
	print glob.glob(r'../*.py') #相对路径  
	
import glob #获取指定目录下的所有图片 print glob.glob(r"E:\Picture\*\*.jpg") #获取上级目录的所有.py文件 print glob.glob(r'../*.py') #相对路径



### iglob
获取一个可编历对象，使用它可以逐个获取匹配的文件路径名。与glob.glob()的区别是：glob.glob同时获取所有的匹配路径，而 glob.iglob一次只获取一个匹配路径。这有点类似于.NET中操作数据库用到的DataSet与DataReader。下面是一个简单的例子：
	
	import glob  
	#父目录中的.py文件  
	f = glob.iglob(r'../*.py')  
	print f #<generator object iglob at 0x00B9FF80>  
	for py in f:  
	    print py  

import glob #父目录中的.py文件 f = glob.iglob(r'../*.py') print f #<generator object iglob at 0x00B9FF80> for py in f: print py
