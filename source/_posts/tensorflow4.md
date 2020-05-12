---
title: tensorflow 反向传播
date: 2018-09-08 15:19:45
categories:
- [人工智能,深度学习,框架,tensorflow]
tags:
- machine learning
- tensorflow
---
这一章主要讲述反向传播。

<!-- more -->

反向传播的作用是训练模型参数，在所有参数上用梯度下降，使用神经网络模型在训练数据上的损失函数最小。

## 损失函数（loss）

预测值 y 和已知答案 y_ 的差距

一般主要有以下形式

### 均方误差（MSE）

![](/images/tensorflow/2_0.JPG)

```python
loss = tf.reduce_mean(tf.square(y_ - y))
```

## 训练方法

以减小 loss 值为优化目标

```python
train_step = tf.train.GradientDescentOptimizer(learning_rate).minimize(loss)
train_step = tf.train.MomentumOptimizer(learning_rate,momentum).minimize(loss)
train_step = tf.train.AdamOptimizer(learning_rate).minimize(loss)
```

## 学习率

决定参数每次更新的幅度

```python
# 导入模块，生成模拟数据集
import tensorflow as tf
import numpy as np
BATCH_SIZE = 8
seed = 23455

# 基于 seed 产生随机数
rng = np.random.RandomState(seed)
# 随机数返回 32 行 2 列 的矩阵 表示 32 组 体积和重量 作为输入数据集
X = rng.rand(32,2)

# 从 X 这个 32 行 2 列 的矩阵中 取出一行 判断如果和小于 1 给 Y 赋值 1 如果 和不小于 1 给 Y 赋值 0
# 作为输入数据集的标签
Y = [[int(X0 + X1 < 1)] for (X0,X1) in X]
		# 在 python 中 true 为 1，false 为 0

#1 定义神经网络的输入，参数和输入，定义前向传播过程
x = tf.placeholder(tf.float32,shape=(None,2))
y_ = tf.placeholder(tf.float32,shape=(None,1))

w1 = tf.Variable(tf.random_normal([2,3],stddev=1,seed=1))
w2 = tf.Variable(tf.random_normal([3,1],stddev=1,seed=1))

a = tf.matmul(x,w1)
y = tf.matmul(a,w2)

#2 定义损失函数及反向传播方法
loss = tf.reduce_mean(tf.square(y - y_))
train_step = tf.train.GradientDescentOptimizer(0.001).minimize(loss)

#3 生成会话，训练 steps 轮
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)
	# 输出目前（未经训练）的参数取值
	print(sess.run(w1))
	print(sess.run(w2))

	# 训练模型
	STEPS = 3000
	for i in range(STEPS):
		# 这是分多次喂入神经网络
		start = (i * BATCH_SIZE) % 32
		end = start + BATCH_SIZE
		sess.run(train_step,feed_dict={x:X[start:end],y_:Y[start:end]})
		if i % 500 == 0:
			total_loss = sess.run(loss,feed_dict={x:X,y_:Y})
			print("After %d training step(s),loss on all data is %g" % (i,total_loss))

		# 输出训练后的参数取值
	print(sess.run(w1))
	print(sess.run(w2))

	
	# [[-0.8113182   1.4845988   0.06532937]
	# [-2.4427042   0.0992484   0.5912243 ]]
	# [[-0.8113182 ]
	# [ 1.4845988 ]
	# [ 0.06532937]]
	# After 0 training step(s),loss on all data is 5.13118
	# After 500 training step(s),loss on all data is 0.429111
	# After 1000 training step(s),loss on all data is 0.409789
	# After 1500 training step(s),loss on all data is 0.399923
	# After 2000 training step(s),loss on all data is 0.394146
	# After 2500 training step(s),loss on all data is 0.390597
	# [[-0.7000663   0.9136318   0.08953571]
	# [-2.3402493  -0.14641267  0.58823055]]
	# [[-0.06024267]
	# [ 0.91956186]
	# [-0.0682071 ]]
```

## 神经网络八股

搭建神经网络八股：准备，前传，反传，迭代

### 准备

优化数据集

### 前向传播

定义输入，参数和输出

### 反向传播

定义损失函数，反向传播方法

### 迭代

生成会话，训练 STEPS 轮