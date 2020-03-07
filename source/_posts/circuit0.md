---
title: 电路
date: 2019-02-16 11:07:52
categories:
- circuit
tags:
- circuit
---
[教程地址](https://www.bilibili.com/video/av25285345)

<!-- more -->

## 组成

circuit

	source（generator，PV cell ，mic[麦克风]）
	load[负载]（motor,speaker,screen）
	Energy or/and signal processing circuits[能量信号处理电路]
		(transformer，filter[滤波器]，amplifier[放大器])
	switch and line[开关和线路]
		(circuit board，transmission line)
		
## 分类

从 load 的角度来分类

如果电路中负载全部都是电阻，那么那个就是 resistive(阻性电路)

如果电路中包含电容元件，那么它就是 Dynamic(动态电路)

从 source 角度分类

如果是直流源，那么是 dc

如果是交流源，那么是 ac， ac 又可以划分为 sine(正弦交流), periodical （周期源，比如方波之类的，也包括 sine）

## 线性关系

"线性"="齐次性"+"可加性",

"齐次性"是指类似于: f(ax)=af(x),

"可加性"是指类似于: f(x+y)=f(x)+f(y),

这里没有太多特别的原因, 就是一个名字. "非线性"当然就是这两条至少之一不成立.

比如

	x = a + 1
	2 * (a + 1) != 2 * x
		不满足齐次行，所以不是线性方程
		
如果一个电路中所有的元件都是线性关系，那么这个电路就是线性电路，否则就是非线性电路。
	