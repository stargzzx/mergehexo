---
title: python 库
date: 2018-06-13 11:29:08
categories:
- python
tags:
- python 第三方库
- python 模块
- python模块
- python库
- python
- tutorial
---
这里是 python 库教程的目录。
<!-- more -->
## 介绍
{% codeblock %}

import 模块名

from 模块名 import 函数名

import 模块名 as 新名字

if __name__ == '__main__':

	可用于测试该模块

比如模块A，在模块A中运行模块A，他的__name__就是__main__

在模块B中导入模块A，A模块的__name__就是A，而不是__main__,B模块的__name__就是__main__

搜索路径

	py导入模块需要一个搜索路径的过程

	即

	import sys

	sys.path 只有模块在path中，py才能找到相应的模块

	可以用sys.path.append("path") 加上相应的搜索路径

包

	创建一个文件夹，存放模块

	在文件夹中创建一个名为

		__init__.py的文件

	将相关模块放入文件夹中

		__init__.py 的目的就是告诉py，这是一个包，可以为空，什么都不写

	import 文件夹名.模块名

{% endcodeblock %}
