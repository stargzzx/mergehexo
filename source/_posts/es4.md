---
title: 遗传算法 GA 交叉和变异详解
date: 2018-10-27 16:37:17
categories:
- 进化算法
tags:
- 进化算法
- GA
---
内容如题所示。
<!-- more -->
# 交叉
交叉有很多种方式，下面来一一介绍。
## 单点交叉
又称为简单交叉，顾名思义，就是指在个体编码串中只随机一个交叉点，然后互换位置。
## 双点交叉和多点交叉
双点交叉就是在编码中随机设置两个交叉点，然后进行基因互换。
{% img /images/machinelearningalgorithm/9_0.png %}
多点交叉也是顾名思义，直接看例子吧。
{% img /images/machinelearningalgorithm/9_1.png %}
## 均匀交叉
个体的每个基因点都有相同的概率被交叉遗传，我之前写的遗传算法的例子都是均匀交叉。
贴一张过程图，加深你们的理解。
{% img /images/machinelearningalgorithm/9_2.png %}
## 算数交叉
直接贴图
{% img /images/machinelearningalgorithm/9_3.png %}
我们可以发现，这次的遗传并不是相互交换了，而是相互融合。所以对象是浮点类型。
也算是很有新意的遗传。
# 变异
变异的目的是增加种群的多样性，而不至于出现比较优秀的个体，造成基因垄断，从而陷入局部最优。
## 基本位变异
对于二进制的编码或者其他编码方式，将某一点的基因变成其它基因点，是最基本的变异方式。
## 均匀变异
{% img /images/machinelearningalgorithm/9_4.png %}
## 边界变异
{% img /images/machinelearningalgorithm/9_5.png %}
这个变异方式就很极端，适合于最优解可能在定义域两端的方式下。
## 非均匀变异
很有意思的变异。
{% img /images/machinelearningalgorithm/9_6.png %}
## 高斯变异
{% img /images/machinelearningalgorithm/9_7.png %}
{% img /images/machinelearningalgorithm/9_8.png %}
交叉和变异这么多种算法并不是单个的孤岛，而是可以相互组合的。




