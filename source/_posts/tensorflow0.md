---
title: tensorflow 基础
date: 2018-08-24 14:51:48
categories:
- tensorflow
tags:
- machine learning
- tensorflow
---
如题所示
<!-- more -->
## tensorflow 的数据到底是什么类型
在 Python 语言中, 返回的 tensor 是 numpy ndarray 对象; 在 C 和 C++ 语言中, 返回的 tensor 是 tensorflow::Tensor 实例.
{% codeblock %}

import tensorflow as tf
import numpy as np
a = tf.constant([[3,3]])
b = tf.constant([[2],[2]])
c = tf.matmul(a,b)
sess = tf.Session()
print(type(sess.run(c)))
	# <class 'numpy.ndarray'>
print(sess.run(c).dtype)
	# int32 只有是 numpy 的数据类型才有 ndarry ，所以，也验证了上面的话
sess.close()

{% endcodeblock %}

## tensor
TensorFlow 程序使用 tensor 数据结构来代表所有的数据, 计算图中, 操作间传递的数据都是 tensor. 你可以把 TensorFlow tensor 看作是一个 n 维的数组或列表. 一个 tensor 包含一个静态类型 rank, 和 一个 shape. 
## +（矩阵相加）
{% codeblock %}

import tensorflow as tf
a = tf.constant([[1,2,3],
				 [4,5,6]])
b = tf.constant([[4,5,6]])
sess = tf.Session()
print(sess.run(a + b))
	# [[ 5  7  9]
	#	[ 8 10 12]]

{% endcodeblock %}
## hello world
{% codeblock %}

# 导入模块，生成模拟数据集
import tensorflow as tf
import numpy as np

# 创造数据
x_data = np.random.rand(100).astype(np.float32)
y_data = x_data * 0.1 + 0.3

# 开始创建结构
# 随机生成 W ，Variable是指参数，里面的 tf.random_uniform 表示随机生成，其中里面的 [1] 代表 1 维， -1.0 到 1.0 代表取值范围
Weights = tf.Variable(tf.random_uniform([1],-1.0,1.0))
biases = tf.Variable(tf.zeros([1]))

# 要预测的函数
y = Weights * x_data + biases

# 损失函数
loss = tf.reduce_mean(tf.square(y - y_data))

# 优化方法，即梯度下降 0.5 是学习效率
optimizer = tf.train.GradientDescentOptimizer(0.5)
# 神经网络要减少这个误差，也就是下面这个函数会不断地优化 W 和 B
train = optimizer.minimize(loss)

# 虽然前面已经定义了很多变量，但是我们必须得用下面这句话去初始化
init = tf.initialize_all_variables()

# 定义会话
# 会话我们可以理解为一个指针，只要这个指针指向一个参数，那么这个参数就会被激活
sess = tf.Session()
# 直接激活初始化的所有变量
sess.run(init)
for step in range(201):
    sess.run(train)
    if step % 20 == 0:
        print(step,sess.run(Weights),sess.run(biases))
	# 0 [0.01949972] [0.47387725]
	# 20 [0.06144673] [0.3206523]
	# 40 [0.08898389] [0.30590114]
	# 60 [0.09685231] [0.30168617]
	# 80 [0.0991006] [0.3004818]
	# 100 [0.09974301] [0.30013767]
	# 120 [0.09992658] [0.30003935]
	# 140 [0.09997901] [0.30001125]
	# 160 [0.09999399] [0.30000323]
	# 180 [0.09999829] [0.30000094]
	# 200 [0.09999952] [0.30000028]
	
{% endcodeblock %}