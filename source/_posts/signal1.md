---
title: signal 基础
date: 2019-03-04 11:51:29
categories:
- signal
tags:
- signal
- 信号处理
- 信号基础
---
这篇主要讲述了信号的频率等相关属性。

<!--more-->

## 参考资料

[振幅、周期、相移和频率](https://www.shuxuele.com/algebra/amplitude-period-frequency-phase-shift.html)

## 周期

周期是从一个最高点到下一个最高点（或任何一点到下一个相对点）

振幅是从中（平）线到最高点的高度（或到最低点），也是从最高点到最低点的距离除以2。

![](/images/signal/1_10.png)

相移是函数比通常的位置水平向右移了多远。

![](/images/signal/1_11.png)

垂直位移是函数比通常的位置垂直向上移了多远。

![](/images/signal/1_12.png)

全部一起来！

我们可以全部放进一个方程里：

	y = A sin(Bx + C) + D

	振幅是A
	周期是2π/B
	相移是−C/B
	垂直移位是D

![](/images/signal/1_13.png)
![](/images/signal/1_14.png)
注意：相移的公式 −C/B有负号：
若C为正值，曲线向负方向（左）移
若C为负值，曲线向正方向（右）移
有时我们用 t 而不用 x （也可能用其他变量）：
![](/images/signal/1_15.png)
## 频率
频率是在一个时间单位里发生多少次（每 "1"）。
![](/images/signal/1_16.png)
其实周期和频率是相连的：

	频率 =  1/周期
	周期 =  1/频率
	
![](/images/signal/1_17.png)
当频率的单位是每秒时，这个单位便称做" 赫兹"。
例子： 50 赫兹的意思是每秒50次（每秒50周）







