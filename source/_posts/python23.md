---
title: Python数据可视化利器Matplotlib，colors系列，颜色的指定形式
date: 2018-10-31 14:59:12
categories:
- python
- 模块
- matplotlib
tags:
- python
- tutorial
- matplotlib
---
[Python数据可视化利器Matplotlib，colors系列，颜色的指定形式](https://blog.csdn.net/m0_37235489/article/details/79761450)

<!-- more -->

当我们绘图的时候，经常要指定各种各样的颜色，如何才能让matplotlib准确的显示出我们想要的颜色呢？这就要求我们必须了解matplotlib可以识别的颜色的指定形式有哪些。

	灰度值字符串（ color = '0.75' ）

灰度值取值范围为区间[0,1]的浮点数，在使用时注意写成字符串形式。
	
	RGB、RGBA元组（ color = (1,1,1) ）

matplotlib中的RGB、RGBA元组与我们常见的RGB或RGBA列表不完全相同，我们常见的RGB或RGBA列表内的数值均为0到255的整数，而在matplotlib中元组内的数值则为区间[0,1]内的浮点数。

	例如，红色：(1,0,0)或(1,0,0,1)；黄色：(1,1,0)或(1,1,0,1)；白色：(1,1,1)或(1,1,1,1)

RGB、RGBA十六进制形式字符串（ color = '#1f77b4' ）

如：'#0F0F0F' 或 '#0F0F0F0F'

常用颜色简写（ color = 'b' ）

	b: 蓝色（blue）
	g: 绿色（green）
	r: 红色（red）
	c: 蓝绿色（cyan）
	m: 紫红色（magenta）
	y: 黄色（yellow）
	k: 黑色（black）
	w: 白色（white）
	X11/CSS4颜色名称（ color = 'skyblue' ）
	xkcd颜色名称（ color = 'xkcd:blue' ）

完整名称列表请查看https://xkcd.com/color/rgb/

Tableau颜色名称（ color = 'tab:blue' ）

颜色列表：

	'blue', '#1f77b4'
	'orange', '#ff7f0e'
	'green', '#2ca02c'
	'red', '#d62728'
	'purple', '#9467bd'
	'brown', '#8c564b'
	'pink', '#e377c2'
	'gray', '#7f7f7f'
	'olive', '#bcbd22'
	'cyan', '#17becf'
	color_cycle列表中的颜色（ color = 'Cn' ）

该列表中的颜色与Tableau中的颜色相同