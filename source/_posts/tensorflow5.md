---
title: tensorflow 神经网络的损失函数
date: 2018-09-13 16:16:13
categories:
- tensorflow
tags:
- machine learning
- tensorflow
---
这一章主要讲解神经网络的优化。

<!-- more -->

{% img /images/tensorflow/3_0.jpg %}

## 激活函数

{% img /images/tensorflow/3_1.jpg %}

## 神经网络的复杂度

层数 = 隐藏层数 + 1 个输出层，特别注意的是，是不包含输入层的

总参数 = 总 W + 总 b

比如输入层有 3 个节点，隐藏层有一层为 4 个节点，输出层有两个节点。

总参数为 3 * 4 + 4 + 4 * 2 + 2 = 26

## 损失函数

部分细节可以参考下面的文章。

[tensorflow 反向传播](https://benpaodewoniu.github.io/2018/09/08/tensorflow2/)

### 主流的损失函数

一共可以分为三类
	
	1. 均方误差
		loss_mse = tf.reduce_mean(tf.square(y_ - y))
	2. 自定义
	3. 交叉熵
	
## 场景模拟

### 场景 1 均方误差损失函数

描述

一个酸奶生产厂，想要预测最佳的生产量。其中 x1 , x2 是影响酸奶的两个因素。

日销量为 y_ 。

为了更加贴近现实，我们队 x1 和 x2 添加正负 0.05 的噪声。

拟合可以预测销量的函数。

代码

{% codeblock %}
# 导入模块，生成模拟数据集
import tensorflow as tf
import numpy as np
BATCH_SIZE = 8
seed = 23455

# 基于 seed 产生随机数
rng = np.random.RandomState(seed)
# 随机数返回 32 行 2 列 的矩阵 表示 32 组 体积和重量 作为输入数据集
X = rng.rand(32,2)
Y_ = [[x1 + x2 + (rng.rand()/10.0 - 0.05)] for (x1,x2) in X ]
	# 拟合数据
	# rng.rand() 生成 [0,1) 随机数

#1 定义神经网络的输入，参数和输入，定义前向传播过程
x = tf.placeholder(tf.float32,shape=(None,2))
y_ = tf.placeholder(tf.float32,shape=(None,1))

w1 = tf.Variable(tf.random_normal([2,1],stddev=1,seed=1))

y = tf.matmul(x,w1)

#2 定义损失函数及反向传播方法
loss = tf.reduce_mean(tf.square(y - y_))
train_step = tf.train.GradientDescentOptimizer(0.001).minimize(loss)

#3 生成会话，训练 steps 轮
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)

	# 训练模型
	STEPS = 20000
	for i in range(STEPS):
		start = (i * BATCH_SIZE) % 32
		end = start + BATCH_SIZE
		sess.run(train_step,feed_dict={x:X[start:end],y_:Y_[start:end]})
		if i % 500 == 0:
			total_loss = sess.run(loss,feed_dict={x:X,y_:Y_})
			print(sess.run(w1))
			print("After %d training step(s),loss on all data is %g" % (i,total_loss))

# [[-0.80974597]
#  [ 1.4852903 ]]
# After 0 training step(s),loss on all data is 0.655701
# [[-0.46074435]
#  [ 1.641878  ]]
# After 500 training step(s),loss on all data is 0.35731
# [[-0.21939856]
#  [ 1.6984766 ]]
# After 1000 training step(s),loss on all data is 0.232481
# [[-0.04415595]
#  [ 1.7003176 ]]
# After 1500 training step(s),loss on all data is 0.170404
# [[0.08942621]
#  [1.673328  ]]
# After 2000 training step(s),loss on all data is 0.133037
# [[0.19583555]
#  [1.6322677 ]]
# After 2500 training step(s),loss on all data is 0.106939
# [[0.28375748]
#  [1.5854434 ]]
# After 3000 training step(s),loss on all data is 0.0870619
# [[0.35848638]
#  [1.5374472 ]]
# After 3500 training step(s),loss on all data is 0.0712709
# [[0.42332518]
#  [1.4907393 ]]
# After 4000 training step(s),loss on all data is 0.0584907
# [[0.48040026]
#  [1.4465574 ]]
# After 4500 training step(s),loss on all data is 0.0480653
# [[0.53113604]
#  [1.4054536 ]]
# After 5000 training step(s),loss on all data is 0.0395331
# [[0.5765325]
#  [1.3675941]]
# After 5500 training step(s),loss on all data is 0.0325409
# [[0.61732584]
#  [1.3329403 ]]
# After 6000 training step(s),loss on all data is 0.0268078
# [[0.6540846]
#  [1.3013426]]
# After 6500 training step(s),loss on all data is 0.0221059
# [[0.6872685]
#  [1.272602 ]]
# After 7000 training step(s),loss on all data is 0.0182493
# [[0.71725976]
#  [1.2465005 ]]
# After 7500 training step(s),loss on all data is 0.015086
# [[0.7443861]
#  [1.2228197]]
# After 8000 training step(s),loss on all data is 0.0124914
# [[0.7689324]
#  [1.2013483]]
# After 8500 training step(s),loss on all data is 0.0103631
# [[0.79115134]
#  [1.1818889 ]]
# After 9000 training step(s),loss on all data is 0.00861742
# [[0.811267 ]
#  [1.1642567]]
# After 9500 training step(s),loss on all data is 0.00718553
# [[0.8294814]
#  [1.1482829]]
# After 10000 training step(s),loss on all data is 0.006011
# [[0.84597576]
#  [1.1338125 ]]
# After 10500 training step(s),loss on all data is 0.00504758
# [[0.8609128]
#  [1.1207061]]
# After 11000 training step(s),loss on all data is 0.00425734
# [[0.87444043]
#  [1.1088346 ]]
# After 11500 training step(s),loss on all data is 0.00360914
# [[0.88669145]
#  [1.0980824 ]]
# After 12000 training step(s),loss on all data is 0.00307745
# [[0.8977863]
#  [1.0883439]]
# After 12500 training step(s),loss on all data is 0.00264134
# [[0.9078348]
#  [1.0795243]]
# After 13000 training step(s),loss on all data is 0.00228362
# [[0.91693527]
#  [1.0715363 ]]
# After 13500 training step(s),loss on all data is 0.00199021
# [[0.92517716]
#  [1.0643018 ]]
# After 14000 training step(s),loss on all data is 0.00174954
# [[0.93264157]
#  [1.0577497 ]]
# After 14500 training step(s),loss on all data is 0.00155213
# [[0.9394023]
#  [1.0518153]]
# After 15000 training step(s),loss on all data is 0.00139019
# [[0.9455251]
#  [1.0464406]]
# After 15500 training step(s),loss on all data is 0.00125737
# [[0.95107025]
#  [1.0415728 ]]
# After 16000 training step(s),loss on all data is 0.00114842
# [[0.9560928]
#  [1.037164 ]]
# After 16500 training step(s),loss on all data is 0.00105905
# [[0.96064115]
#  [1.0331714 ]]
# After 17000 training step(s),loss on all data is 0.000985753
# [[0.96476096]
#  [1.0295546 ]]
# After 17500 training step(s),loss on all data is 0.000925622
# [[0.9684917]
#  [1.0262802]]
# After 18000 training step(s),loss on all data is 0.00087631
# [[0.9718707]
#  [1.0233142]]
# After 18500 training step(s),loss on all data is 0.000835858
# [[0.974931 ]
#  [1.0206276]]
# After 19000 training step(s),loss on all data is 0.000802676
# [[0.9777026]
#  [1.0181949]]
# After 19500 training step(s),loss on all data is 0.000775461
{% endcodeblock %}

结论

逻辑上我们可以得出，当销量和产量平齐的时候，企业能获得最大的利润，所以，我们可以看到参数慢慢趋近于 1，而 loss 也越来越小。

### 场景二 自定义损失函数

描述

还是接上一个例子，虽然均方误差能够使得产量和销量相平齐，但是无法使得利益最大化。

比如，预测多了，成本会增加，预测少了，利润会减少。

所以我们要自定义损失函数，公式如下：

{% img /images/tensorflow/3_2.JPG %}

{% img /images/tensorflow/3_3.JPG %}

在 tf 中，具体代码如下：

	loss = tf.reduce_sum(tf.where(tf.greater(y,y_),(y - y_) * COST,(y_ - y) * PROFIT))
	tf.where(tf.greater(y,y_) 相当于一个三元选择器 即 y > y_ ? ，如果式子条件成立则选择前面，如果不成立则选择后面
	
代码1

{% codeblock %}
# 导入模块，生成模拟数据集
import tensorflow as tf
import numpy as np
BATCH_SIZE = 8
seed = 23455
COST = 1	# 成本
PROFIT = 9 # 利润

# 基于 seed 产生随机数
rng = np.random.RandomState(seed)
# 随机数返回 32 行 2 列 的矩阵 表示 32 组 体积和重量 作为输入数据集
X = rng.rand(32,2)
Y_ = [[x1 + x2 + (rng.rand()/10.0 - 0.05)] for (x1,x2) in X ]
	# rng.rand() 生成 [0,1) 随机数

#1 定义神经网络的输入，参数和输入，定义前向传播过程
x = tf.placeholder(tf.float32,shape=(None,2))
y_ = tf.placeholder(tf.float32,shape=(None,1))

w1 = tf.Variable(tf.random_normal([2,1],stddev=1,seed=1))

y = tf.matmul(x,w1)

#2 定义损失函数及反向传播方法
loss = tf.reduce_sum(tf.where(tf.greater(y,y_),(y - y_) * COST,(y_ - y) * PROFIT))
train_step = tf.train.GradientDescentOptimizer(0.001).minimize(loss)

#3 生成会话，训练 steps 轮
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)

	# 训练模型
	STEPS = 20000
	for i in range(STEPS):
		start = (i * BATCH_SIZE) % 32
		end = start + BATCH_SIZE
		sess.run(train_step,feed_dict={x:X[start:end],y_:Y_[start:end]})
		if i % 500 == 0:
			total_loss = sess.run(loss,feed_dict={x:X,y_:Y_})
			print(sess.run(w1))
			print("After %d training step(s),loss on all data is %g" % (i,total_loss))

# [[-0.762993 ]
#  [ 1.5095658]]
# After 0 training step(s),loss on all data is 178.826
# [[1.0235443]
#  [1.0463386]]
# After 500 training step(s),loss on all data is 1.93054
# [[1.0174844]
#  [1.0406483]]
# After 1000 training step(s),loss on all data is 1.92153
# [[1.0211805]
#  [1.0472497]]
# After 1500 training step(s),loss on all data is 1.92689
# [[1.0179386]
#  [1.0412899]]
# After 2000 training step(s),loss on all data is 1.91444
# [[1.0205938]
#  [1.0390677]]
# After 2500 training step(s),loss on all data is 1.91977
# [[1.0242898]
#  [1.0456691]]
# After 3000 training step(s),loss on all data is 1.92866
# [[1.01823  ]
#  [1.0399789]]
# After 3500 training step(s),loss on all data is 1.91861
# [[1.021926 ]
#  [1.0465802]]
# After 4000 training step(s),loss on all data is 1.92529
# [[1.0245812]
#  [1.044358 ]]
# After 4500 training step(s),loss on all data is 1.92074
# [[1.0185213]
#  [1.0386678]]
# After 5000 training step(s),loss on all data is 1.92279
# [[1.0245652]
#  [1.0446368]]
# After 5500 training step(s),loss on all data is 1.92261
# [[1.0185053]
#  [1.0389466]]
# After 6000 training step(s),loss on all data is 1.92151
# [[1.0222014]
#  [1.045548 ]]
# After 6500 training step(s),loss on all data is 1.91924
# [[1.0161415]
#  [1.0398577]]
# After 7000 training step(s),loss on all data is 1.93691
# [[1.0198376]
#  [1.0464591]]
# After 7500 training step(s),loss on all data is 1.91587
# [[1.0224928]
#  [1.0442369]]
# After 8000 training step(s),loss on all data is 1.91131
# [[1.0174738]
#  [1.0473702]]
# After 8500 training step(s),loss on all data is 1.91249
# [[1.0222716]
#  [1.0383747]]
# After 9000 training step(s),loss on all data is 1.92229
# [[1.0172527]
#  [1.041508 ]]
# After 9500 training step(s),loss on all data is 1.91914
# [[1.0199078]
#  [1.0392858]]
# After 10000 training step(s),loss on all data is 1.919
# [[1.0236039]
#  [1.0458871]]
# After 10500 training step(s),loss on all data is 1.92736
# [[1.017544 ]
#  [1.0401969]]
# After 11000 training step(s),loss on all data is 1.92332
# [[1.0212401]
#  [1.0467982]]
# After 11500 training step(s),loss on all data is 1.92399
# [[1.0238953]
#  [1.044576 ]]
# After 12000 training step(s),loss on all data is 1.91943
# [[1.0178354]
#  [1.0388858]]
# After 12500 training step(s),loss on all data is 1.92749
# [[1.0215315]
#  [1.0454872]]
# After 13000 training step(s),loss on all data is 1.91606
# [[1.0154716]
#  [1.039797 ]]
# After 13500 training step(s),loss on all data is 1.94288
# [[1.0191677]
#  [1.0463983]]
# After 14000 training step(s),loss on all data is 1.91269
# [[1.0162914]
#  [1.0427582]]
# After 14500 training step(s),loss on all data is 1.92095
# [[1.0189465]
#  [1.040536 ]]
# After 15000 training step(s),loss on all data is 1.91422
# [[1.0216017]
#  [1.0383139]]
# After 15500 training step(s),loss on all data is 1.92261
# [[1.0252978]
#  [1.0449152]]
# After 16000 training step(s),loss on all data is 1.92756
# [[1.0192379]
#  [1.039225 ]]
# After 16500 training step(s),loss on all data is 1.91931
# [[1.022934 ]
#  [1.0458263]]
# After 17000 training step(s),loss on all data is 1.92419
# [[1.0168741]
#  [1.0401361]]
# After 17500 training step(s),loss on all data is 1.92929
# [[1.0205702]
#  [1.0467374]]
# After 18000 training step(s),loss on all data is 1.92081
# [[1.0232253]
#  [1.0445153]]
# After 18500 training step(s),loss on all data is 1.91626
# [[1.0171654]
#  [1.038825 ]]
# After 19000 training step(s),loss on all data is 1.93347
# [[1.0208615]
#  [1.0454264]]
# After 19500 training step(s),loss on all data is 1.91289
{% endcodeblock %}

结论2

系数都偏大

代码2

我们修改参数，让成本为 9，利润为 1

{% codeblock %}
# 导入模块，生成模拟数据集
import tensorflow as tf
import numpy as np
BATCH_SIZE = 8
seed = 23455
COST = 9
PROFIT = 1

# 基于 seed 产生随机数
rng = np.random.RandomState(seed)
# 随机数返回 32 行 2 列 的矩阵 表示 32 组 体积和重量 作为输入数据集
X = rng.rand(32,2)
Y_ = [[x1 + x2 + (rng.rand()/10.0 - 0.05)] for (x1,x2) in X ]
	# rng.rand() 生成 [0,1) 随机数

#1 定义神经网络的输入，参数和输入，定义前向传播过程
x = tf.placeholder(tf.float32,shape=(None,2))
y_ = tf.placeholder(tf.float32,shape=(None,1))

w1 = tf.Variable(tf.random_normal([2,1],stddev=1,seed=1))

y = tf.matmul(x,w1)

#2 定义损失函数及反向传播方法
loss = tf.reduce_sum(tf.where(tf.greater(y,y_),(y - y_) * COST,(y_ - y) * PROFIT))
train_step = tf.train.GradientDescentOptimizer(0.001).minimize(loss)

#3 生成会话，训练 steps 轮
with tf.Session() as sess:
	init_op = tf.global_variables_initializer()
	sess.run(init_op)

	# 训练模型
	STEPS = 20000
	for i in range(STEPS):
		start = (i * BATCH_SIZE) % 32
		end = start + BATCH_SIZE
		sess.run(train_step,feed_dict={x:X[start:end],y_:Y_[start:end]})
		if i % 500 == 0:
			total_loss = sess.run(loss,feed_dict={x:X,y_:Y_})
			print(sess.run(w1))
			print("After %d training step(s),loss on all data is %g" % (i,total_loss))

# [[-0.80594873]
#  [ 1.4873729 ]]
# After 0 training step(s),loss on all data is 29.8745
# [[0.8732146]
#  [1.006204 ]]
# After 500 training step(s),loss on all data is 2.38814
# [[0.9658064]
#  [0.9698208]]
# After 1000 training step(s),loss on all data is 1.46394
# [[0.9645447]
#  [0.9682946]]
# After 1500 training step(s),loss on all data is 1.46911
# [[0.9602475]
#  [0.9742084]]
# After 2000 training step(s),loss on all data is 1.43915
# [[0.96100295]
#  [0.96993417]]
# After 2500 training step(s),loss on all data is 1.45907
# [[0.9654102]
#  [0.9761159]]
# After 3000 training step(s),loss on all data is 1.43333
# [[0.96414846]
#  [0.9745897 ]]
# After 3500 training step(s),loss on all data is 1.43359
# [[0.95985126]
#  [0.9805035 ]]
# After 4000 training step(s),loss on all data is 1.44526
# [[0.9636422]
#  [0.9687893]]
# After 4500 training step(s),loss on all data is 1.46411
# [[0.959345 ]
#  [0.9747031]]
# After 5000 training step(s),loss on all data is 1.44187
# [[0.9667877]
#  [0.9734448]]
# After 5500 training step(s),loss on all data is 1.44656
# [[0.9641995]
#  [0.9676626]]
# After 6000 training step(s),loss on all data is 1.4717
# [[0.9655712 ]
#  [0.98128426]]
# After 6500 training step(s),loss on all data is 1.48756
# [[0.9653083]
#  [0.9817019]]
# After 7000 training step(s),loss on all data is 1.48998
# [[0.9670821 ]
#  [0.97273576]]
# After 7500 training step(s),loss on all data is 1.4512
# [[0.964802 ]
#  [0.9759015]]
# After 8000 training step(s),loss on all data is 1.42806
# [[0.9658656]
#  [0.9805752]]
# After 8500 training step(s),loss on all data is 1.48214
# [[0.9646039]
#  [0.979049 ]]
# After 9000 training step(s),loss on all data is 1.45434
# [[0.96303403]
#  [0.9685749 ]]
# After 9500 training step(s),loss on all data is 1.46374
# [[0.95873684]
#  [0.9744887 ]]
# After 10000 training step(s),loss on all data is 1.44686
# [[0.9598004]
#  [0.9791624]]
# After 10500 training step(s),loss on all data is 1.43406
# [[0.966935 ]
#  [0.9689562]]
# After 11000 training step(s),loss on all data is 1.47155
# [[0.95929414]
#  [0.97336197]]
# After 11500 training step(s),loss on all data is 1.4499
# [[0.96004957]
#  [0.9690877 ]]
# After 12000 training step(s),loss on all data is 1.46982
# [[0.9600948 ]
#  [0.97845334]]
# After 12500 training step(s),loss on all data is 1.43006
# [[0.96085024]
#  [0.9741791 ]]
# After 13000 training step(s),loss on all data is 1.4356
# [[0.9649493 ]
#  [0.97141284]]
# After 13500 training step(s),loss on all data is 1.45304
# [[0.96499455]
#  [0.98077846]]
# After 14000 training step(s),loss on all data is 1.47699
# [[0.96373284]
#  [0.9792523 ]]
# After 14500 training step(s),loss on all data is 1.4492
# [[0.962163  ]
#  [0.96877813]]
# After 15000 training step(s),loss on all data is 1.46042
# [[0.9601911]
#  [0.9808918]]
# After 15500 training step(s),loss on all data is 1.44943
# [[0.95892936]
#  [0.97936565]]
# After 16000 training step(s),loss on all data is 1.43981
# [[0.9660639]
#  [0.9691594]]
# After 16500 training step(s),loss on all data is 1.46822
# [[0.9604402 ]
#  [0.97081715]]
# After 17000 training step(s),loss on all data is 1.45746
# [[0.96484745]
#  [0.97699887]]
# After 17500 training step(s),loss on all data is 1.43351
# [[0.9642764 ]
#  [0.96846855]]
# After 18000 training step(s),loss on all data is 1.46748
# [[0.9599792 ]
#  [0.97438234]]
# After 18500 training step(s),loss on all data is 1.4398
# [[0.9630599]
#  [0.976308 ]]
# After 19000 training step(s),loss on all data is 1.42141
# [[0.967159  ]
#  [0.97354174]]
# After 19500 training step(s),loss on all data is 1.44697
{% endcodeblock %}

结论2

系数都小于 1

### 场景模拟 3 交叉熵

交叉熵表征两个概率分布之间的距离，公式如下：

{% img /images/tensorflow/3_4.JPG %}

交叉熵越小，两个概率分布越近，就越接近正确答案。

例子：

	已知答案，y_ = (1,0) 预测 Y1 = (0.6,0.4) Y2 = (0.8,0.2) 哪个更接近标准答案？
	H1((1,0),(0.6,0.4)) = -(1 * log0.6 + 0 * log0.4) = 0.222
	H2((1,0),(0.8,0.2)) = -(1 * log0.8 + 0 * log0.2) = 0.097
	所以 Y2 更加准一些
	
在 tf 中的表现

	ce = -tf.reduce_mean(y_ * tf.log(tf.clip_by_value(y,1e-12,1.0)))
	# 意思就是 y 小于 1e-12 为 1e-12 大于 1.0 为 1.0，这是为了防止数据出错，因为是 log 函数，所以不存在 log0 或者 log负数，另外，因为是概率问题，所以输入都是[0,1] 之间的数。
	
数据的制作
	
	我们知道，对一件事的发生，有很多种可能，即有很多种概率，但这些概率有一个前提，就是加起来等于 1。
	所以我们要使用 softmax() 函数来制造这类数据
	
公式如下：

{% img /images/tensorflow/3_5.JPG %}

在 tf 中的代码

	ce = tf.nn.sparse_softmax_cross_entropy_with_logits(logits = y,labels = y,labels=tf.argmax(y_,1))
	cem = tf.reduce_mean(ce)
	




