---
title: tensorflow 张量，计算图，会话
date: 2018-08-26 14:51:48
categories:
- [人工智能,深度学习,框架,tensorflow]
tags:
- machine learning
- tensorflow
---
这一章主要讲述张量、计算图、会话

<!-- more -->

tensorflow 中的 tensor 就是张量的意思。

在 tensorflow 中，用张量来表示数据，用计算图来搭建神经网络，用会话执行计算图，然后优化权重，最后得到模型。

## 张量

张量实际上就是多维数组。在这里要知道一个说法，即阶表示张量的维数。

	维数	阶		名字	例子
	0-D		0		标量	S = 1 2 3
	1-D		1		向量	S = [1 ,2 ,3]
	2-D		2		矩阵	S = [[1,2,3],[4,5,6]]
	n-D		n		张量	S = [[[]]] (有几个中括号就表示几阶)

张量可以表示 0 阶到 n 阶数组。

张量的数据类型有： tf.float32 ,tf.int32

### 定义张量

```python
import tensorflow as tf
a = tf.constant([1.0,2.0])
b = tf.constant([3.0,4.0])
result = a + b
print result
	# Tensor("add:0", shape=(2,), dtype=float32)
	# 之所以会输出这种结果，是因为会话这一步才开始计算，其他都是搭建或者表示
	# add 表示运算方式 shape 表示维度 dtype 表示数据类型
```

## 计算图

搭建神经网络的计算过程，只搭建，不计算

比如

![](/images/tensorflow/0_0.png)

即：Y = XW = X1 * W1 + X2 * W2

```python
import tensorflow as tf
X = tf.constant([[1.0,2.0]])	# 表示建立一个 1 * 2 的张量
W = tf.constant([[3.0],[4.0]])	# 表示建立一个 2 * 1 的张量
result = tf.matmul(X,W)
print(result)
	# Tensor("MatMul:0", shape=(1, 1), dtype=float32)
	# 之所以会输出这种结果，是因为会话这一步才开始计算，其他都是搭建或者表示
	# add 表示运算方式 shape 表示维度 dtype 表示数据类型	
```

## 会话

session 是执行计算图中的节点运算。

第一种方式如下

	sess = tf.Session()
	result = sess.run(product)
	sess.close()

第二种方式如下

	with tf.Session() as sess:
		print(sess.run(Y))
		
```python
import tensorflow as tf
X = tf.constant([[1.0,2.0]])	# 表示建立一个 2 * 1 的张量
W = tf.constant([[3.0],[4.0]])	# 表示建立一个 1 * 2 的张量
result = tf.matmul(X,W)
print(result)
	# Tensor("MatMul:0", shape=(1, 1), dtype=float32)
	# 之所以会输出这种结果，是因为会话这一步才开始计算，其他都是搭建或者表示
	# add 表示运算方式 shape 表示维度 dtype 表示数据类型
with tf.Session() as sess:
	print(sess.run(result))		#[[11.]]
```

### 为什么会有会话？

Session（会话）是tensorflow里面的重要机制，tensorflow构建的计算图必须通过Session才能执行，如果只是在计算图中定义了图的节点但没有使用Session的话，就不能运行该节点。

例如我们在tensorflow中定义了两个矩阵a和b，和一个计算a和b乘积的节点c，如果想要得到a和b的乘积（也就是c节点的运算结果)的话，在构建好计算图以后，还必须要建立Session会话，并调用Session中的run方法运行c节点才行。

为什么要这样“多此一举”呢？

我们知道，为了保证计算效率，TensorFlow 底层是使用C++实现的。而我们客户端却使用python，java等语言在上层设计和定义模型，构建数据流图（data flow graphs）。于是tensorflow就使用 tf.Session这样一个类来连接客户端与底层C++的运行。在我们上层的模型构建好以后，通过tf.Session.run()方法将计算图传递给底层，底层将计算任务分配给不同的CPU、GPU甚至不同的机器中。

### 会话的注意事项

我们得初始化变量，但是初始化得时候，应该先初始化 Session

否则会出错

比如

下面这个是对的

```python
**** # 前面的一些代码
test = cnn(data)
y = [1]
loss=tf.nn.sparse_softmax_cross_entropy_with_logits(logits=test, labels=y)
train_op=tf.train.AdamOptimizer(learning_rate=0.00001).minimize(loss)
with tf.Session() as sess:
    init = tf.global_variables_initializer()
    sess.run(init)
    error = sess.run(loss)
```

但是，如果 init 换位置，下面就会出错

```python
**** # 前面的一些代码
test = cnn(data)
y = [1]
init = tf.global_variables_initializer()
loss=tf.nn.sparse_softmax_cross_entropy_with_logits(logits=test, labels=y)
train_op=tf.train.AdamOptimizer(learning_rate=0.00001).minimize(loss)
with tf.Session() as sess:
    sess.run(init)
    error = sess.run(loss)
```













