---
title: tensorflow 前向传播
date: 2018-08-28 15:44:50
categories:
- tensorflow
tags:
- machine learning
- tensorflow
---
这一章主要讲述前向传播。

<!-- more -->

## Variable()

首先介绍 Variable 函数。

	W = tf.Variable(tf.random_normal([2,3],stddev = 2,mean = 0,seed = 1))
	
### tf.random_normal() 的参数解释
	
	random_normal 是产生正态分布
	[2,3]		产生 2 * 3 的矩阵
	stddev = 2	标准差为 2
	mean = 0 	均值为 0
	seed = 1 	随机种子，随机种子如果去掉，每次生成的随机数将不一样
	其中，stddev,mean,seed 可以不写
	
和 tf.random_normal 等价的函数还有

	tf.truncated_normal() 去掉大偏离点的正态分布，如果随机出来的数据超过 2 个标准差，则数据去掉
	tf.random_uniform() 	平均分布
	tf.zeros([3,2],int32)	全是 0 的数组
	tf.ones([3,2],int32)
	tf.fill([3,2],6)	生成[[6,6],[6,6],[6,6]] 全定值数组
	tf.constant([3,2,1]) 生成[3,2,1] 直接给值
	
## 神经网络实现过程

1. 准备数据集，提取特征，作为输入喂给神经网络
2. 搭建神经网络，从输入到输出（先搭建计算图，再用会话执行）
3. 大量特征数据喂给神经网络，迭代优化神经网络参数
4. 使用训练好的模型预测和分类

举个例子：

{% img /images/tensorflow/1_0.png %}

代码：

{% codeblock %}
import tensorflow as tf
# 定义输入和参数
x = tf.constant([[0.7,0.5]])
W1 = tf.Variable(tf.random_normal([2,3],stddev = 1 ,seed = 1))
W2 = tf.Variable(tf.random_normal([3,1],stddev = 1 ,seed = 1))

#定义前向传播过程
a = tf.matmul(x,W1)
y = tf.matmul(a,W2)

#用会话计算结果
with tf.Session() as sess:
	init_op = tf.global_variables_initializer() # 实现对所有变量的初始化，官方定义
	sess.run(init_op)			# 官方定义
	print("y is",sess.run(y))
		# y is [[3.0904665]]
{% endcodeblock %}

但是有时候我们不知道到底有多少数据。

所以，可以用 tf.placeholder 占位,在 sess.run 函数中用 feed_dict 喂数据

	喂一组数据
	x = tf.placeholder(tf.float32,shape(1,2))
	sess.run(y,feed_dict = {x:[[0.5,0.6]]})
	
	喂多组数据
	x = tf.placeholder(tf.float32,shape = (None,2)) # 不知道具体有多少数据，所以用 None 代替行数， 2 表示两个特征
	sess.run(y,feed_dict = {x:[[0.1,0.2],[0.2,0.3]})

代码（喂一组数据）：

{% codeblock %}
import tensorflow as tf
# 定义输入和参数
x = tf.placeholder(tf.float32,shape=(1,2))
W1 = tf.Variable(tf.random_normal([2,3],stddev = 1 ,seed = 1))
W2 = tf.Variable(tf.random_normal([3,1],stddev = 1 ,seed = 1))

#定义前向传播过程
a = tf.matmul(x,W1)
y = tf.matmul(a,W2)

#用会话计算结果
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)
	print("y is",sess.run(y,feed_dict={x:[[0.7,0.5]]}))
		# y is [[3.0904665]]
{% endcodeblock %}

代码（喂多组数据）：

{% codeblock %}
import tensorflow as tf
# 定义输入和参数
x = tf.placeholder(tf.float32,shape=(None,2))
W1 = tf.Variable(tf.random_normal([2,3],stddev = 1 ,seed = 1))
W2 = tf.Variable(tf.random_normal([3,1],stddev = 1 ,seed = 1))

#定义前向传播过程
a = tf.matmul(x,W1)
y = tf.matmul(a,W2)

#用会话计算结果
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)
	print("y is",sess.run(y,feed_dict={x:[[0.7,0.5],[0.4,0.5]]}))
		#y is [[3.0904665]
				[2.2305048]]
	print("W1 is ",sess.run(W1)) # 打印中间值
{% endcodeblock %}

### global_variables_initializer()

当我们训练自己的神经网络的时候，无一例外的就是都会加上一句 sess.run(tf.global_variables_initializer()) ，这行代码的官方解释是 初始化模型的参数。那么，它到底做了些什么？

global_variables_initializer 返回一个用来初始化 计算图中 所有global variable的 op。 

这个op 到底是啥，还不清楚。

函数中调用了 variable_initializer() 和 global_variables()

global_variables() 返回一个 Variable list ，里面保存的是 gloabal variables。

variable_initializer() 将 Variable list 中的所有 Variable 取出来，将其 variable.initializer 属性做成一个 op group。

然后看 Variable 类的源码可以发现， variable.initializer 就是一个 assign op。

所以： sess.run(tf.global_variables_initializer()) 就是 run了 所有global Variable 的 assign op，这就是初始化参数的本来面目。

