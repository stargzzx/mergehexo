---
title: tensorflow 学习率
date: 2018-09-24 17:53:58
categories:
- tensorflow
tags:
- machine learning
- tensorflow
---
这篇主要讲解学习率。

<!-- more -->

学习率 learning_rate 即为每次参数更新的幅度。

![](/images/tensorflow/4_0.JPG)

关于梯度的事情我会在另一篇文章中详细说明。

## 例子

### 学习率等于 0.2 时

```python
import tensorflow as tf
# 设定损失函数 loss = (w + 1) ^ 2 ,令 w 初值为 5。反向传播就是求最优 w ，即求最小 loss 对应的 w 即 w 等于 -1 的时候
#定义待优化参数 W 初值为 5
w = tf.Variable(tf.constant(5,dtype = tf.float32))

# 定义损失函数 loss
loss = tf.square(w + 1)

# 定义反向传播方法
train_step = tf.train.GradientDescentOptimizer(0.2).minimize(loss)

# 生成会话，训练 40 轮
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)
	for i in range(40):
		sess.run(train_step)
		w_val = sess.run(w)
		print(w_val)

			#。。。
			#-0.99999994
			#-0.99999994
			#-0.99999994
			#-0.99999994	
```

## 注意点

在上面的代码中，我摘抄一下来一段

```python
for i in range(40):
		sess.run(train_step)
		w_val = sess.run(w)	
```

tensorflow 就好像是乐高积木，建立一个金字塔，并不是只需要最后一块砖，而是每一层的砖都需要，如果改成下面的代码，那么 w 会一直都是 5。

```python
for i in range(40):
		w_val = sess.run(w)
```


### 学习率等于 1 时

```python
import tensorflow as tf
# 设定损失函数 loss = (w + 1) ^ 2 ,令 w 初值为 5。反向传播就是求最优 w ，即求最小 loss 对应的 w 即 w 等于 -1 的时候
#定义待优化参数 W 初值为 5
w = tf.Variable(tf.constant(5,dtype = tf.float32))

# 定义损失函数 loss
loss = tf.square(w + 1)

# 定义反向传播方法
train_step = tf.train.GradientDescentOptimizer(1).minimize(loss)

# 生成会话，训练 40 轮
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)
	for i in range(40):
		sess.run(train_step)
		w_val = sess.run(w)
		print(w_val)

			#。。。
			#5.0
			#-7.0
			#5.0
			#-7.0
			#5.0	
```

关于学习率的远离我就不说了，有时间我就专门写个文章

## 动态学习率

没怎么看懂。。。不理解

我们在上述看到，学习率如果是一成不变的，那么效率绝对不会是最优，甚至还有可能帮倒忙。

那么在 tensorflow 中如何规划动态学习率？

学习率大了震荡不收敛，学习率小了收敛速度慢。

### 指数衰减学习率

learning_rate = LEARNING_RATE_BASE * LEARNING_RATE_DECAY

LEARNING_RATE_BASE : 学习率的初始值

LEARNING_RATE_DECAY ： 学习率的衰减率（0,1） = 总样本数 / BATCH_SIZE（运行了多少轮）

BATCH_SIZE = global_step / LEARNING_RATE_STEP

即在 tensor 中的函数为：

global_step = tf.Variable(0,trainable=False)

learning_rate = tf.train.exponential_decay(LEARNING_RATE_BASE,global_step,LEARNING_RATE_STEP,LEARNING_RATE_DECAY,staircase=True/False)

LEARNING_RATE_BASE : 学习率的基数，也就是初始学习率

global_step:第几轮的计数器

LEARNING_RATE_STEP : 学习率多少轮更新一次

LEARNING_RATE_DECAY： 输入数据集总数 / 每次喂入多少个数据

staircase 为 True global_step / LEARNING_RATE_STEP 取整数，学习率快速梯形衰减，为 False 为平滑下降

```python
import tensorflow as tf
# 设定损失函数 loss = (w + 1) ^ 2 ,令 w 初值为 5。反向传播就是求最优 w ，即求最小 loss 对应的 w
#定义待优化参数 W 初值为 5

LEARNING_RATE_BASE = 0.1 #最初学习率
LEARNING_RATE_DECAY = 0.99 # 学斜率的衰减率
LEARNING_RATE_STEP = 1 # 喂入多少轮 BATCH_SIZE 后，更新一次学习率，一般设为：总样本数 / BATCH_SIZE

# 运行了几轮 BATCH_SIZE 的计数器，初始值给 0，设为不被训练
global_step = tf.Variable(0,trainable=False)

# 定义指数下降学习率
learning_rate = tf.train.exponential_decay(LEARNING_RATE_BASE,global_step,LEARNING_RATE_STEP,LEARNING_RATE_DECAY,staircase=True)
# 定义待优化参数，初始值给 10
w = tf.Variable(tf.constant(5,dtype=tf.float32))
# 定义损失函数 loss
loss = tf.square(w + 1)

# 定义反向传播方法
train_step = tf.train.GradientDescentOptimizer(learning_rate).minimize(loss,global_step=global_step)

# 生成会话，训练 40 轮
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)
	for i in range(40):
		sess.run(train_step)
		learning_rate_val = sess.run(learning_rate)
		global_step_val = sess.run(global_step)
		w_val = sess.run(w)
		loss_val = sess.run(loss)
		print(learning_rate_val,global_step_val,w_val,loss_val)
			
			# 0.099 1 3.8 23.04
			# 0.09801 2 2.8495998 14.819419
			# 0.0970299 3 2.0950012 9.579033
			# 0.096059605 4 1.494386 6.2219615
			# ...
			# 0.069641344 36 -0.9922965 5.9343653e-05
			# 0.06894493 37 -0.99336946 4.396406e-05
			# 0.068255484 38 -0.99428374 3.2675678e-05
			# 0.06757293 39 -0.9950641 2.436331e-05
			# 0.066897206 40 -0.9957312 1.8222867e-05		
```

