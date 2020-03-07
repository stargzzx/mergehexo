---
title: tf 中的’SAME’和’VALID’填充有什么区别？
date: 2019-01-29 09:10:17
categories:
- tensorflow
tags:
- nn模块
- machine learning
- tensorflow
- same
- valid
---
如题所示。

<!-- more -->

## 参考资料

[python – 张量流的tf.nn.max_pool中的’SAME’和’VALID’填充有什么区别？](https://codeday.me/bug/20180130/126838.html)

## 正解

{% img /images/tensorflow/18_1.png %}

直接看代码

图象是 2 * 3

{% codeblock %}
x = tf.constant([[1., 2., 3.],
				[4., 5., 6.]])

x = tf.reshape(x, [1, 2, 3, 1])  # give a shape accepted by tf.nn.max_pool

valid_pad = tf.nn.max_pool(x, [1, 2, 2, 1], [1, 2, 2, 1], padding='VALID')
same_pad = tf.nn.max_pool(x, [1, 2, 2, 1], [1, 2, 2, 1], padding='SAME')

valid_pad.get_shape() == [1, 1, 1, 1]  # valid_pad is [5.]
same_pad.get_shape() == [1, 1, 2, 1]   # same_pad is  [5., 6.]
{% endcodeblock %}

图象是 3 * 2

{% codeblock %}
x = tf.constant([[1., 2., 3.],
				[4., 5., 6.]])

x = tf.reshape(x, [1, 3, 2, 1])  # give a shape accepted by tf.nn.max_pool

valid_pad = tf.nn.max_pool(x, [1, 2, 2, 1], [1, 2, 2, 1], padding='VALID')
same_pad = tf.nn.max_pool(x, [1, 2, 2, 1], [1, 2, 2, 1], padding='SAME')

valid_pad.get_shape() == [1, 1, 1, 1]  # valid_pad is [5.]
same_pad.get_shape() == [1, 2, 1, 1]   # same_pad is  [5., 6.]
{% endcodeblock %}

图象是 3 * 3

{% codeblock %}
import tensorflow as tf

x = tf.constant([[1., 2., 3.],
				[4., 5., 6.],
				[7.,8.,9.]])

x = tf.reshape(x, [1, 3, 3, 1])  # give a shape accepted by tf.nn.max_pool

valid_pad = tf.nn.max_pool(x, [1, 2, 2, 1], [1, 2, 2, 1], padding='VALID')
same_pad = tf.nn.max_pool(x, [1, 2, 2, 1], [1, 2, 2, 1], padding='SAME')
print(valid_pad.get_shape()) # (1,1,1,1)
print(same_pad.get_shape()) # (1,2,2,1)
{% endcodeblock %}




