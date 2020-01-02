---
title: tensorflow 滑动平均
date: 2018-10-08 21:23:58
categories:
- tensorflow
tags:
- machine learning
- tensorflow
---
如题，我也是第一次见滑动平均这个概念。
<!-- more -->
滑动平均是一个优化方法。滑动平均又叫影子值，记录了每个参数一段时间内过往值的平均，增加了模型的泛化性。
滑动平均针对所有参数（W 和 b），就好像是给参数加了影子，参数变化，影子缓慢追随。

	影子 = 衰减率 * 影子 + （1 - 衰减率） * 参数
	影子初值 = 参数初值
	衰减率 = min{MOVING_AVERAGE_DECAY，（1 + 轮数）/ （10 + 轮数）}
	MOVING_AVERAGE_DECAY： 一个常参数
	
参数变化

	初始定义：
	MOVING_AVERAGE_DECAY 为 0.99
	参数 w1 为 0
	轮数 global_step 为 0
	w1 的滑动平均值为 0
	参数 w1 更新为 1 则：
	w1 滑动平均值 = min(0.99,1/10) * 0 + (1 - min(0.99,1/10)) * 1 = 0.9
	轮数 global_step 为 100 时，参数 w1 更新为 10 则：
	w1 滑动平均值 = min(0.99,101/110) * 0.9 + (1 - min(0.99,101/110)) * 10 = 1.644
	再次运行
	w1 滑动平均值 = min(0.99,101/110) * 1.644 + (1 - min(0.99,101/110)) * 10 = 2.328
	...
	
## 在 tensorflow 中如何写滑动平均
{% codeblock %}

ema = tf.train.ExponentialMovingAverage(衰减率MOVING_AVERAGE_DECAY,当前轮数gloal_step)
#ema_op = ema.apply([])
ema_op = ema.apply(tf.trainable_variables())
	#每运行此句，所有待优化的参数求滑动平均
with tf.control_dependencies([train_step,ema_op]):
	train_op = tf.no_op(name = 'train')
ema.average(参数名)
	# 查看某些参数的活动平均值
	
{% endcodeblock %}

## 代码示例
{% codeblock %}

import tensorflow as tf

#1 定义一个 32 位浮点变量，初始值为 0.0 这个代码就是不断地更新 w1 参数，优化 w1 参数，滑动平均做了个 w1 的影子
w1 = tf.Variable(0,dtype=tf.float32)
# 定义 num_updates(NN的迭代轮数)，初始值为 0，不可被优化（训练），这个参数不训练
global_step = tf.Variable(0,trainable=False)
# 实例化滑动平均类，给衰减率赋值 0.99 ，当前轮数 global_step
MOVING_AVERAGE_DECAY = 0.99
ema = tf.train.ExponentialMovingAverage(MOVING_AVERAGE_DECAY,global_step)
# ema.apply 后的括号里面是更新列表，每次运行 sess.run(ema_op)时，对更新列表中的元素求滑动平均值
# 在实际应用中会使用 tf.trainable_variables() 自动将所有待训练的参数汇总成为列表
#ema_op = ema.apply([w1])
ema_op = ema.apply(tf.trainable_variables())

#2 查看不同迭代中变量取值的变化
with tf.Session() as sess:
	# 初始化
	init_op = tf.global_variables_initializer()
	sess.run(init_op)
	# 用 ema.average(w1) 获取 w1 滑动平均值（要运行多个节点，作为列表中元素的列出，写在 sess.run 中）
	# 打印出当前参数 w1 和 w1 滑动平均
	print(sess.run([w1,ema.average(w1)]))

	# 参数 w1 的值赋为 1
	sess.run(tf.assign(w1,1))
	sess.run(ema_op)
	print(sess.run([w1,ema.average(w1)]))

	# 更新 step 和 w1 的值，模拟出 100 轮迭代后，参数 w1 变为 10
	sess.run(tf.assign(global_step,100))
	sess.run(tf.assign(w1,10))
	sess.run(ema_op)
	print(sess.run([w1,ema.average(w1)]))

	#  每次 sess.run 会更新一次 w1 的滑动平均值
	sess.run(ema_op)
	print(sess.run([w1,ema.average(w1)]))

	sess.run(ema_op)
	print(sess.run([w1, ema.average(w1)]))

	sess.run(ema_op)
	print(sess.run([w1, ema.average(w1)]))

	sess.run(ema_op)
	print(sess.run([w1, ema.average(w1)]))

	sess.run(ema_op)
	print(sess.run([w1, ema.average(w1)]))

	sess.run(ema_op)
	print(sess.run([w1, ema.average(w1)]))
		
		# [0.0, 0.0]
		# [1.0, 0.9]
		# [10.0, 1.6445453]
		# [10.0, 2.3281732]
		# [10.0, 2.955868]
		# [10.0, 3.532206]
		# [10.0, 4.061389]
		# [10.0, 4.547275]
		# [10.0, 4.9934072]
		
{% endcodeblock %}
