---
title: hexo | 多级分类
date: 2020-03-21 18:40:22
categories:
- hexo
tags:
- hexo
---
当你的内容提量越来越大的时候，你可能迫切的需要一个合理的内容分类。

而，这一点，我们可以用多级目录来解决。

<!-- more -->

想象一下，这样一个场景，你的其中一个分类是 python ，但是， python 里面包括基础、进阶还与其他。

如果都只用一个 python 分类的话，以后会变得越来越庞杂。

所以，一级目录是 python 二级目录是 基础、进阶、其他，三级是。。。

<br/>

# 父子分类

<br/>

这个可以用下面的形式解决

	categories:
	- python
	- 模块

这样写的形式如下面所示

- python
	- 模块

要记住，以 \- 的分类是父子关系，不是同级关系。

<br/>

# 同级分类

<br/>

如果要使用同级关系，需要这样

	categories:
	- [python,模块,PIL]
	- [python,模块,matplotlib]

这样的效果如下

- python
	- 模块
		- PIL
		- matplotlib

这篇文章同时在 PIL 和 matplotlib 子分类下。