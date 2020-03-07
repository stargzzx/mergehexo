---
title: vim编辑器用法
date: 2019-10-03 20:46:43
categories:
- MacBook
tags:
- MacBook
- system
- linux
- vim
---
这是vim编辑器的一些用法。

<!-- more -->

# readonly option is set

用vi修改文件，保存文件时，提示“readonly option is set”的解决方法。

步骤：

	1.按Esc键
	2.输入  :set noreadonly
	3.然后就能正常保存了，你可以输入 :wq 来保存文件了。
	
