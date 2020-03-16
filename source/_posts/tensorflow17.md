---
title: loss 总是 NAN
date: 2019-01-28 15:10:11
categories:
- tensorflow
tags:
- loss
- machine learning
- tensorflow
mathjax: true
---
这个问题困扰了我很长时间，大概半小时吧，经过修改参数和查阅资料，现在记录如下。

<!-- more -->

## 参考资料

[为什么用tensorflow训练网络，出现了loss=nan，accuracy总是一个固定值？ - 王赟 Maigo的回答 - 知乎](https://www.zhihu.com/question/62441748/answer/232522878)

## 问题描述

我用交叉熵作为损失函数，经过训练之后，loss 呈现 Nan 。

具体的原因是下面的代码。

```python
test1 = -tf.reduce_sum(y_*tf.log(y),reduction_indices=[1,0])
test2 = -tf.reduce_sum(y_*tf.log(y))

loss1 = -tf.reduce_sum(y_*tf.log(y),reduction_indices=[1,0]) / 50.0 # batch 的大小是 50
loss2 = tf.reduce_mean(-tf.reduce_sum(y_*tf.log(y),reduction_indices=[1]))

optimizer = tf.train.GradientDescentOptimizer(0.5)

train1 = optimizer.minimize(test1)
train2 = optimizer.minimize(test2)
train3 = optimizer.minimize(loss1)
train4 = optimizer.minimize(loss2)
```

让人感到惊奇的是，用 train1，train2 训练的 loss 是 nan ，而用 train3,train4 训练的却是正常的。

这可真是玄学。

但是如果改变学习率，比如
	
	optimizer = tf.train.GradientDescentOptimizer(0.01)
	
那么所有的都是正常的。

## 结合知乎上的回答做总结

>1.最常见的原因是学习率太高。对于分类问题，学习率太高会导致模型「顽固」地认为某些数据属于错误的类，而正确的类的概率为 0（实际是浮点数下溢），这样用交叉熵就会算出无穷大的损失函数。一旦出现这种情况，无穷大对参数求导就会变成 NaN，之后整个网络的参数就都变成 NaN 了。
解决方法是调小学习率，甚至把学习率调成 0，看看问题是否仍然存在。若问题消失，那说明确实是学习率的问题。若问题仍存在，那说明刚刚初始化的网络就已经挂掉了，很可能是实现有错误。

>2.loss函数用的什么函数？对于分类问题，用categorical cross entropy

>3.对于回归问题，可能出现了除0 的计算，加一个很小的余项可能可以解决

>4.数据本身，是否存在Nan，可以用numpy.any(numpy.isnan(x))检查一下input和target

>5.target本身应该是能够被loss函数计算的，比如sigmoid激活函数的target应该大于0，同样的需要检查数据集

>6.按照你的描述，假设是一个分类问题的loss。如果你是自己实现的交叉熵损失函数，且直接按照公式实现 $ -ylog(y’) - (1 - y)log(1 - y’) $ ，那么很有可能是计算sigmoid的时候exp(-x)溢出导致loss出现nan。所以要么直接用tf官方的交叉熵函数，要么按照tf官方交叉熵的写法自己实现。
[tf官方交叉熵](https://www.tensorflow.org/api_docs/python/tf/nn/sigmoid_cross_entropy_with_logits)

>7.有时候可以先用较小的学习率训练5000或以上次迭代，得到参数输出，手动kill掉训练，用前面的参数fine tune，这时候可以加大学习率，能更快收敛哦














