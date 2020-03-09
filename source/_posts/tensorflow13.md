---
title: MNIST
date: 2018-11-13 15:15:49
categories:
- tensorflow
tags:
- machine learning
- tensorflow
- MNIST
---
这只是一个简单的 tensorflow 分类例子。

<!-- more -->

## MNIST 数据

在 tensorflow 的例子中自带 MNIST 数据包，我们可以直接引用，如果我们的 tensorflow 没有这个包，那么它会自己下载的。如果有数据的话，它会直接读取。

![](/images/tensorflow/13_0.png)

相关代码如下;

{% codeblock %}
from tensorflow.examples.tutorials.mnist import input_data
mnist = input_data.read_data_sets("MNIST_data/", one_hot=True)
{% endcodeblock %}

但是，我们应该自己下载下来。[因为漫长的等待总是让人崩溃的]

[MNIST 数据下载](http://www.tensorfly.cn/tfdoc/tutorials/mnist_download.html)

我们可以用迅雷将资源下载下来，放到文件中。

目录如下：

![](/images/tensorflow/13_1.png)


## 参考 tensorflow 的文档写的代码

用最普通的方式做训练

{% codeblock %}
import tensorflow as tf
from tensorflow.examples.tutorials.mnist import input_data
import numpy as np
import time

mnist = input_data.read_data_sets('MNIST_data/',one_hot=True)

x = tf.placeholder(tf.float32,shape=[None,784])
y_ = tf.placeholder(tf.float32,shape=[None,10])
# 构造一个线性模型
b = tf.Variable(tf.zeros([10]))
W = tf.Variable(tf.random_uniform([784, 10], -1.0, 1.0))
y = tf.nn.softmax(tf.matmul(x,W) + b)

# 最小化方差
loss = tf.reduce_mean(-tf.reduce_sum(y_*tf.log(y),reduction_indices=[1]))
optimizer = tf.train.GradientDescentOptimizer(0.5)
train = optimizer.minimize(loss)

# 初始化变量
init = tf.global_variables_initializer()

# 启动图 (graph)
sess = tf.Session()
sess.run(init)
batch = mnist.train.next_batch(50)
correct_prediction = tf.equal(tf.argmax(y,1),tf.argmax(y_,1))
accuracy = tf.reduce_mean(tf.cast(correct_prediction,tf.float32))

# 拟合平面
for step in range(1500):
    batch = mnist.train.next_batch(50)
    train.run(session=sess, feed_dict={x: batch[0], y_: batch[1]})
    print(sess.run(loss, feed_dict={x: batch[0], y_: batch[1]}))
    print("test accuracy %g" % accuracy.eval(session=sess,feed_dict={x:mnist.test.images,y_:mnist.test.labels}))

{% endcodeblock %}

## 用 CNN 做训练
{% codeblock %}

from tensorflow.examples.tutorials.mnist import input_data
import tensorflow as tf
 
#初始化权重函数
def weight_variable(shape):
    initial = tf.truncated_normal(shape,stddev=0.1);
    return tf.Variable(initial)
 
#初始化偏置项
def bias_variable(shape):
    initial = tf.constant(0.1,shape=shape)
    return tf.Variable(initial)
 
#定义卷积函数
def conv2d(x,w):
    return tf.nn.conv2d(x,w,strides=[1,1,1,1],padding='SAME')
 
#定义一个2*2的最大池化层
def max_pool_2_2(x):
    return tf.nn.max_pool(x,ksize=[1,2,2,1],strides=[1,2,2,1],padding='SAME')
 
if __name__ == "__main__":
    #定义输入变量
    x = tf.placeholder("float",shape=[None,784])
    #定义输出变量
    y_ = tf.placeholder("float",shape=[None,10])
    #初始化权重,第一层卷积，32的意思代表的是输出32个通道
    # 其实，也就是设置32个卷积，每一个卷积都会对图像进行卷积操作
    w_conv1 = weight_variable([5,5,1,32])
    #初始化偏置项
    b_conv1 = bias_variable([32])
    #将输入的x转成一个4D向量，第2、3维对应图片的宽高，最后一维代表图片的颜色通道数
    # 输入的图像为灰度图，所以通道数为1，如果是RGB图，通道数为3
    # tf.reshape(x,[-1,28,28,1])的意思是将x自动转换成28*28*1的数组
    # -1的意思是代表不知道x的shape，它会按照后面的设置进行转换
    x_image = tf.reshape(x,[-1,28,28,1])
    # 卷积并激活
    h_conv1 = tf.nn.relu(conv2d(x_image,w_conv1) + b_conv1)
    #池化
    h_pool1 = max_pool_2_2(h_conv1)
    #第二层卷积
    #初始权重
    w_conv2 = weight_variable([5,5,32,64])
    #初始化偏置项
    b_conv2 = bias_variable([64])
    #将第一层卷积池化后的结果作为第二层卷积的输入
    h_conv2 = tf.nn.relu(conv2d(h_pool1,w_conv2) + b_conv2)
    #池化
    h_pool2 = max_pool_2_2(h_conv2)
    # 设置全连接层的权重
    w_fc1 = weight_variable([7*7*64,1024])
    # 设置全连接层的偏置
    b_fc1 = bias_variable([1024])
    # 将第二层卷积池化后的结果，转成一个7*7*64的数组
    h_pool2_flat = tf.reshape(h_pool2,[-1,7*7*64])
    # 通过全连接之后并激活
    h_fc1 = tf.nn.relu(tf.matmul(h_pool2_flat,w_fc1) + b_fc1)
    # 防止过拟合
    keep_prob = tf.placeholder("float")
    h_fc1_drop = tf.nn.dropout(h_fc1,keep_prob)
 
    #输出层
    w_fc2 = weight_variable([1024,10])
    b_fc2 = bias_variable([10])
 
    y_conv = tf.nn.softmax(tf.matmul(h_fc1_drop,w_fc2) + b_fc2)
 
    #日志输出，每迭代100次输出一次日志
    #定义交叉熵为损失函数
    cross_entropy = -tf.reduce_sum(y_ * tf.log(y_conv))
    #最小化交叉熵
    train_step = tf.train.AdamOptimizer(1e-4).minimize(cross_entropy)
    #计算准确率
    correct_prediction = tf.equal(tf.argmax(y_conv,1),tf.argmax(y_,1))
    accuracy = tf.reduce_mean(tf.cast(correct_prediction,"float"))
    sess = tf.Session()
    sess.run(tf.initialize_all_variables())
    # 下载minist的手写数字的数据集
    mnist = input_data.read_data_sets("MNIST_data/", one_hot=True)
    for i in range(20000):
        batch = mnist.train.next_batch(50)
        if i % 100 == 0:
            train_accuracy = accuracy.eval(session=sess,feed_dict={x:batch[0],y_:batch[1],keep_prob:1.0})
            print("step %d,training accuracy %g"%(i,train_accuracy))
        train_step.run(session = sess,feed_dict={x:batch[0],y_:batch[1],keep_prob:0.5})
 
    print("test accuracy %g" % accuracy.eval(session=sess,feed_dict={
        x: mnist.test.images, y_: mnist.test.labels, keep_prob: 1.0}))
    #test accuracy 0.9919
{% endcodeblock %}






