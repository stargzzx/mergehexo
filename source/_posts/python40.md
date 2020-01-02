---
title: numpy pandas scipy 的简单区别
date: 2018-12-19 13:44:31
categories:
- python
tags:
- python 第三方库
- python
- numpy
- pandas
- scipy
---
如题。
<!-- more -->
Numpy是以矩阵为基础的数学计算模块，纯数学。
Scipy基于Numpy，科学计算库，有一些高阶抽象和物理模型。比方说做个傅立叶变换，这是纯数学的，用Numpy；
做个滤波器，这属于信号处理模型了，在Scipy里找。
Pandas提供了一套名为DataFrame的数据结构，比较契合统计分析中的表结构，并且提供了计算接口，可用Numpy或其它方式进行计算。



















