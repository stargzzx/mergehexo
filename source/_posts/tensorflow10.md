---
title: 创建神经层
date: 2018-11-09 16:26:11
categories:
- tensorflow
tags:
- machine learning
- tensorflow
- 神经层
---
这个应该是重点。。。

<!-- more -->

一段代码讲述如何建立。。。

```python
import tensorflow as tf

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
```

OK，打完收工。



