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
	4.:w 保存但不退出（常用）
	5.:w! 若文件属性为『只读』时，强制写入该档案
	6.:q 离开 vi （常用）
	7.:q! 若曾修改过档案，又不想储存，使用!为强制离开不储存档案。
	8.:e! 将档案还原到最原始的状态！
	
