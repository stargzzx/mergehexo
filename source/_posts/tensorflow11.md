---
title: 建造神经网络
date: 2018-11-09 16:42:00
categories:
- tensorflow
tags:
- machine learning
- tensorflow
- 神经层
---
简单地介绍一下神经网络的搭建，但是原理可能不太符合吴恩达的原理，但是也无所谓了。
<!-- more -->
{% codeblock %}

import tensorflow as tf
import numpy as np
import time

# 这个代码是莫烦的课程的现场演示
# 据我对机器学习的理解来说，这段代码所演示的原理是错的
# 因为它的偏置是直接加上去的，而不是一个调节作用
# 但是这都是无所谓的，因为这段代码就是让你理解 tensorflow 的流程

# 我来总结一下整个神经网络的流程
# 首先我们有 300 * 1 的输入数据，然后我们经过输入层到隐藏层这一端
# 工作是 300 * 1 的输入数据 * 1 * 10 的权重，再加上 1 * 10 的偏置，返回 300 * 10 的过渡数据
# 从隐藏层到输出层是 300 * 10 的过渡数据 * 10 * 1 的权重 + 1 * 1的偏置
def add_layer(inputs,in_size,out_size,activation_function = None):
    Weights = tf.Variable(tf.random_normal([in_size,out_size]))
    # 推荐偏置初始不为 0
    biases = tf.Variable(tf.zeros([1,out_size]) + 0.1)
    Wx_plus_b = tf.matmul(inputs,Weights) + biases
    if activation_function is None:
        outputs = Wx_plus_b
    else:
        outputs = activation_function(Wx_plus_b)
    return outputs

# x_data 是 (300,1)
x_data = np.linspace(-1,1,300)[:,np.newaxis]
noise = np.random.normal(0,0.05,x_data.shape)
y_data = np.square(x_data) - 0.5 + noise

xs = tf.placeholder(tf.float32,[None,1.])
ys = tf.placeholder(tf.float32,[None,1.])

# 我们要创建的网络结构如下，一层输入，一层隐藏，一层输出
# 其中，输入层只有 1 个神经元
#      隐藏层有 10 个神经元
#      输出层有 1 个神经元

# l1 是 (300,10)
l1 = add_layer(xs,1,10,activation_function=tf.nn.relu)
prediction = add_layer(l1,10,1,activation_function=None)

# loss 是损失函数
loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),reduction_indices=[1]))

train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)

init = tf.initialize_all_variables()
sess = tf.Session()
sess.run(init)

for _ in range(1000):
    sess.run(train_step,feed_dict={xs:x_data,ys:y_data})
    if _ % 50 == 0:
        print(sess.run(loss,feed_dict={xs:x_data,ys:y_data}))


{% endcodeblock %}


