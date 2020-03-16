---
title: python re 模块
date: 2018-06-08 12:19:36
categories:
- python
tags:
- re模块
- python 模块
- python
- tutorial
---
python 中 re 模块。

<!-- more -->

## re

这里还要引入我的另一篇正则表达式博文。

[正则表达式](https://benpaodewoniu.github.io/2018/06/13/basis4/)

```python
import re ： python正则表达式模块
一次性函数：
re.search(pattern,string,flags = 0) 返回match对象
	在一个字符串中查找匹配,匹配第一个位置，只能查找一个
	flags:正则表达式使用时的控制标记，有三个
		re.I 忽略正则表达式中的大小写，[A-Z]可以匹配小写字符
		re.M 正则表达式中^操作符能够将给定的字符串每行当做匹配的开始
		re.S 正则表达式中的.操作符能够匹配所有字符，除了换行符
re.match(pattern,string,flags = 0) 返回match对象

re.findall(pattern,string,flags = 0)
	找到匹配，返回所有匹配部分的列表，能查找所有

re.split(pattern,string,maxspilt = 0,flags = 0)
	根据匹配分割字符串，返回列表类型
	maxspilt: 最大分割数，剩余部分作为最后一个元素输出

re.finditer(pattern,string,flags)
	搜索字符串，返回一个匹配结果的迭代类型，每个迭代元素是match对象

re.sub(pattern,repl,string,count=0,flags=0)
	将字符串中匹配正则表达式的部分替换为其他值
		repl：替换匹配字符串的字符串
		string： 待匹配的字符串
		count：匹配的最大替换次数

面向对象用法，编译后多次操作，上面的都属于一次性用法，正则表达式只能用一次

re.compile(pattern,flags = 0)
	将正则表达式的字符串形式编译成正则表达式对象

pat = re.compile(r'immoc') 以 immoc 为开头，compile 是创建一个 pattern 加 r 代表是一个源字符串
			（r'immoc\n'）  =  ('immoc\\n')

ret = pat.search('待搜索的字符串')
同样也可以调用sub，spilt等等上述一次性函数

match对象用法
	一次匹配的结果
	属性
		string 待匹配的文本 
		re 匹配时使用的pattern对象（正则表达式）
		pos 正则表达式搜索文本的开始位置
		endpos 正则表达式搜索文本的结束位置

	方法
		group(0) 获得匹配后的字符串
			其他group（）用法参考正则表达式
		start（） 匹配字符串在原始字符串的开始位置
		end（）	匹配字符串在原始字符串的结束位置
		span（） 返回（start（），end（））
```
