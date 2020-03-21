---
title: python 编码异常
date: 2019-02-23 10:46:12
categories:
- python
- 基础
tags:
- python
- 编码
- 错误
- error
- 编码异常
- 异常
---
这里记录了 python 的编码异常。

<!-- more -->

<br/>

# 参考资料

<br/>

[成功解决Python3版UnicodeDecodeError: 'ascii' codec can't decode byte 0x90 in position 614: ordinal not in](https://blog.csdn.net/qq_41185868/article/details/79039604)

[]()

<br/>

# 小知识

<br/>

Python 3默认是utf8编码格式

<br/>

# 异常

<br/>

## UnicodeDecodeError: 'ascii' codec can't decode byte 0xe8 in position

参考资料

[python问题：UnicodeDecodeError: 'ascii' codec can't decode byte 0xe8 in position](https://blog.csdn.net/jewelsu/article/details/78683024)
[解决unicodedecodeerror ascii codec can’t decode byte 0xd7 in position 9 ordinal not in range](http://www.ttlsa.com/python/solve-unicodedecodeerror-ascii-codec-can-not-decode-byte-0xd7-in-position-9-ordinal-not-in-range/)
[Python 的reload 方法](https://blog.csdn.net/yu1150/article/details/78393900)
[Python3中reload的使用方式](https://blog.csdn.net/u011816283/article/details/79242415)
[修改python默认的编码方式](http://www.cnblogs.com/harrychinese/archive/2012/01/19/change_python_default_encoding.html)
[python编码错误：UnicodeDecodeError: 'utf8' codec can't decode](https://blog.csdn.net/fk103/article/details/54233680)
[成功解决Python3版UnicodeDecodeError: 'ascii' codec can't decode byte 0x90 in position 614: ordinal not in](https://blog.csdn.net/qq_41185868/article/details/79039604)

### 背景

用的环境是 python3.5

因为要做脑电项目，方向是情感分类，所以下载了 DEAP 数据库，在 load 数据库的时候出现上面的异常。

	x = cPickle.load(open('s01.dat','rb'))
	
关于这个库的使用，可以参照我下面的博文。

[python 的 cPickle pickle](https://benpaodewoniu.github.io/2019/02/23/python54/)

编码问题，大概是 ascii 不行，所以应该转为 utf-8

于是在 import 下面添加如下的代码

	if sys.getdefaultencoding() != 'utf-8':
		reload(sys)
		sys.setdefaultencoding('utf-8')
		
出现了一个问题，就是没有找到 reload

所以，在开头加入

	import sys
	from imp import reload
	
发现并没有什么卵用，还是编码错误，于是尝试改变编码 gbk

	if sys.getdefaultencoding() != 'gbk':
		reload(sys)
		sys.setdefaultencoding('gbk')

结果出现 name reload’ is not defined

经过查阅资料得知

在3.x中已经被毙掉了被替换为

	import importlib
	importlib.reload(sys)

但是，也没有用这个代码，因为我尝试输出了一下原始编码

	print(sys.getdefaultencoding())
	
发现默认编码就是 utf-8

经过，一段时间的排查，将

	x = cPickle.load(open('s01.dat','rb'))
	
变为

	x = cPickle.load(open('s01.dat','rb'),encoding='bytes')
	
问题解决。也就是在 python3 的环境中不需要修改编码。

### 后续

sys.setdefaultencoding 方法在python导入 site.py 后就删除了, 不能再被调用了.  在确定sys已经导入的情况下, 可以reload sys这个模块, 之后, 再 sys.setdefaultencoding('utf8')

当然，这个 setdefaultencoding 在python3.x 已经转变为其他书写方式了。

但是，还有更好的方法

如何永久地将默认编码设置为utf-8呢?  有2种方法: 

第一个方法<不推荐>: 

编辑你的文件，按照上面的书写方式修改编码, 也就是setencoding()函数, 强制设置为 utf-8 

第二个方法<推荐>: 

增加一个名为 sitecustomize.py, 推荐存放的路径为 site-packages 目录下

sitecustomize.py 是在 site.py 被import 执行的, 因为 sys.setdefaultencoding() 是在 site.py 的结尾处被删除的, 所以, 可以在 sitecustomize.py 使用 sys.setdefaultencoding(). 

	#file name:  sitecustomize.py
	import sys  
	sys.setdefaultencoding('utf-8')   

既然 sitecustomize.py 能被自动加载,  所以除了设置编码外, 也可以设置一些其他的东西. 

第二种方法没有试过，但是如果要用，我还是选择第一种方法，但考虑到代码复用，也许第二种方法更好。















