---
title: 神经层的可视化 tensorboard
date: 2018-11-12 16:26:21
categories:
- tensorflow
tags:
- tensorboard
- machine learning
- tensorflow
- 可视化
---
这个可以让你建立的神经网络可视化。

你学完这个技能就会感到自己逼格满满，所以，必须得学。

<!-- more -->

tensorboard 是 tensorflow 自带的。

我们先看一下效果。

{% img /images/tensorflow/12_0.png %}

{% img /images/tensorflow/12_1.png %}

是不是非常酷炫，用一种很直观的方式将神经网络图展示出来。

## 对具体数据进行命名

我们看到第二幅图中的数据都是有名字的，这个很容易做到，只需要在变量上使用 name 属性即可。

	xs= tf.placeholder(tf.float32, [None, 1],name='x_in')
	
上面那个是一个小图，我们知道一个神经层中有很多这种参数，那么如果将这些参数放在一个层上呢？

需要注意的是，这个命名是需要点击具体的数据才能打开

{% img /images/tensorflow/12_2.png %}

## 对神经层命名

具体格式如下：

{% codeblock %}
with tf.name_scope('inputs'): # 这句代码就是命名一个神经层
	# define placeholder for inputs to network
	xs = tf.placeholder(tf.float32, [None, 1])
	ys = tf.placeholder(tf.float32, [None, 1])
{% endcodeblock %}

将所有参数放在同一个层，并且给参数也给上相应的层

{% codeblock %}
def add_layer(inputs, in_size, out_size, activation_function=None):
    # add one more layer and return the output of this layer
    with tf.name_scope('layer'):
        with tf.name_scope('weights'):
            Weights = tf.Variable(
            tf.random_normal([in_size, out_size]), 
            name='W')
        with tf.name_scope('biases'):
            biases = tf.Variable(
            tf.zeros([1, out_size]) + 0.1, 
            name='b')
        with tf.name_scope('Wx_plus_b'):
            Wx_plus_b = tf.add(
            tf.matmul(inputs, Weights), 
            biases)
        if activation_function is None:
            outputs = Wx_plus_b
        else:
            outputs = activation_function(Wx_plus_b, )
        return outputs
{% endcodeblock %}

activation_function 的话，可以暂时忽略。因为当你自己选择用 tensorflow 中的激励函数（activation function）的时候，tensorflow会默认添加名称。

## 输出

我们需要使用 tf.summary.FileWriter() (tf.train.SummaryWriter() 这种方式已经在 tf >= 0.12 版本中摒弃) 将上面‘绘画’出的图保存到一个目录中，以方便后期在浏览器中可以浏览。 这个方法中的第二个参数需要使用sess.graph ， 因此我们需要把这句话放在获取session的后面。 这里的graph是将前面定义的框架信息收集起来，然后放在logs/目录下面。

{% codeblock %}、
sess = tf.Session() # get session
# tf.train.SummaryWriter soon be deprecated, use following
writer = tf.summary.FileWriter("logs/", sess.graph)
{% endcodeblock %}

## 查看

最后在你的terminal（终端）中 ，使用以下命令

	tensorboard --logdir logs

这一步要注意的是，你的 tensorboard 应该在 logs 文件夹那一层用，而不是进入 logs 文件夹

## 浏览器查看

根据终端给你的地址，打开浏览器就好。

同时注意, 如果使用 http://0.0.0.0:6006 网址打不开的朋友们, 请使用 http://localhost:6006

并且，图是在 GRAPHS 的选项中。

莫烦的全部代码如下：

{% codeblock %}
# View more python learning tutorial on my Youtube and Youku channel!!!

# Youtube video tutorial: https://www.youtube.com/channel/UCdyjiB5H8Pu7aDTNVXTTpcg
# Youku video tutorial: http://i.youku.com/pythontutorial

"""
Please note, this code is only for python 3+. If you are using python 2+, please modify the code accordingly.
"""
from __future__ import print_function
import tensorflow as tf


def add_layer(inputs, in_size, out_size, activation_function=None):
    # add one more layer and return the output of this layer
    with tf.name_scope('layer'):
        with tf.name_scope('weights'):
            Weights = tf.Variable(tf.random_normal([in_size, out_size]), name='W')
        with tf.name_scope('biases'):
            biases = tf.Variable(tf.zeros([1, out_size]) + 0.1, name='b')
        with tf.name_scope('Wx_plus_b'):
            Wx_plus_b = tf.add(tf.matmul(inputs, Weights), biases)
        if activation_function is None:
            outputs = Wx_plus_b
        else:
            outputs = activation_function(Wx_plus_b, )
        return outputs


# define placeholder for inputs to network
with tf.name_scope('inputs'):
    xs = tf.placeholder(tf.float32, [None, 1], name='x_input')
    ys = tf.placeholder(tf.float32, [None, 1], name='y_input')

# add hidden layer
l1 = add_layer(xs, 1, 10, activation_function=tf.nn.relu)
# add output layer
prediction = add_layer(l1, 10, 1, activation_function=None)

# the error between prediciton and real data
with tf.name_scope('loss'):
    loss = tf.reduce_mean(tf.reduce_sum(tf.square(ys - prediction),
                                        reduction_indices=[1]))

with tf.name_scope('train'):
    train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)

sess = tf.Session()

# tf.train.SummaryWriter soon be deprecated, use following
if int((tf.__version__).split('.')[1]) < 12 and int((tf.__version__).split('.')[0]) < 1:  # tensorflow version < 0.12
    writer = tf.train.SummaryWriter('logs/', sess.graph)
else: # tensorflow version >= 0.12
    writer = tf.summary.FileWriter("logs/", sess.graph)

# tf.initialize_all_variables() no long valid from
# 2017-03-02 if using tensorflow >= 0.12
if int((tf.__version__).split('.')[1]) < 12 and int((tf.__version__).split('.')[0]) < 1:
    init = tf.initialize_all_variables()
else:
    init = tf.global_variables_initializer()
sess.run(init)

# direct to the local dir and run this in terminal:
# $ tensorboard --logdir=logs
{% endcodeblock %}
