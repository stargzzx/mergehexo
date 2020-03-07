---
title: tensorflow 函数详解
date: 2018-08-25 14:51:48
categories:
- tensorflow
tags:
- machine learning
- tensorflow
- 函数详解
---
如题。

<!-- more -->

# A

## add_to_collection

tf.add_to_collection(‘list_name’, element)：将元素element添加到列表list_name中

tf.get_collection(‘list_name’)：返回名称为list_name的列表

tf.add_n(list)：将列表元素相加并返回

{% codeblock %}
import tensorflow as tf
tf.add_to_collection('losses', tf.constant(2.2))
tf.add_to_collection('losses', tf.constant(3.))
with tf.Session() as sess:
    print(sess.run(tf.get_collection('losses')))
    print(sess.run(tf.add_n(tf.get_collection('losses'))
{% endcodeblock %}

	结果：
	[2.2, 3.0] 
	5.2
	注意： 
	使用tf.add_n对列表元素进行相加时，列表内元素类型必须一致，否则会报错。

## all_variables
参照 trainable_variables






# E

## exp()

{% codeblock %}
import tensorflow as tf
x = tf.constant([[1.0,2.0],[5.0,3.0]])
x_2 = tf.exp(x)
with tf.Session() as sess:
    print(sess.run(x_2))
	
		# [[  2.7182817   7.389056 ]
		# [148.41316    20.085537 ]]

{% endcodeblock %}

# G
## get_variable
[tf.get_variable函数的使用](https://blog.csdn.net/uestc_c2_403/article/details/72327321)
[tf.get_variable函数](https://www.w3cschool.cn/tensorflow_python/tensorflow_python-st6f2ez1.html)
tf.get_variable(name,  shape, initializer)[当然还有更多的参数选项]: name就是变量的名称，shape是变量的维度，initializer是变量初始化的方式，初始化的方式有以下几种：

	tf.constant_initializer：常量初始化函数
	tf.random_normal_initializer：正态分布
	tf.truncated_normal_initializer：截取的正态分布
	tf.random_uniform_initializer：均匀分布
	tf.zeros_initializer：全部是0
	tf.ones_initializer：全是1
	tf.uniform_unit_scaling_initializer：满足均匀分布，但不影响输出数量级的随机值

{% codeblock %}

import tensorflow as tf;  
import numpy as np;  
import matplotlib.pyplot as plt;  
  
a1 = tf.get_variable(name='a1', shape=[2,3], initializer=tf.random_normal_initializer(mean=0, stddev=1))
a2 = tf.get_variable(name='a2', shape=[1], initializer=tf.constant_initializer(1))
a3 = tf.get_variable(name='a3', shape=[2,3], initializer=tf.ones_initializer())
 
with tf.Session() as sess:
	sess.run(tf.initialize_all_variables())
	print sess.run(a1)
	print sess.run(a2)
	print sess.run(a3)

		[[ 0.42299312 -0.25459203 -0.88605702]
		[ 0.22410156  1.34326422 -0.39722782]]
		[ 1.]
		[[ 1.  1.  1.]
		[ 1.  1.  1.]]
		
{% endcodeblock %}
注意：不同的变量之间不能有相同的名字，除非你定义了variable_scope，这样才可以有相同的名字。
# M
## matmul
这是矩阵的相乘，相当于 numpy 的 dot 
{% codeblock %}

import tensorflow as tf
matrix1 = tf.constant([[3,3]])
matrix2 = tf.constant([[2],[2]])
product = tf.matmul(matrix1,matrix2)
with tf.Session() as sess:
    result = sess.run(product)
print(result)
	# [[12]]

{% endcodeblock %}




# N
## newaxis
np.newaxis的功能是插入新维度，看下面的例子：
{% codeblock %}

a=np.array([1,2,3,4,5])
b=a[np.newaxis,:]
print a.shape,b.shape
	# (5,) (1, 5)
print a
	# [1 2 3 4 5]
print b
	# [[1 2 3 4 5]]

{% endcodeblock %}
{% codeblock %}

a=np.array([1,2,3,4,5])
b=a[:,np.newaxis]
print a.shape,b.shape
	# (5,) (5, 1)
print a
	# [1 2 3 4 5]
print b
	#  [2]
	#	[3]
	#	[4]
	#	[5]]
{% endcodeblock %}
可以看出np.newaxis分别是在行或列上增加维度，原来是（6，）的数组，在行上增加维度变成（1,6）的二维数组，在列上增加维度变为(6,1)的二维数组




# I
## initialize_all_variables
如果定义了变量，就必须先初始化。
init = tf.initialize_all_variables()
然后在后面激活它

	with tf.Session() as sess:
		sess.run(init)
		

		
		
# P
## placeholder()
这个函数相当于一个占位符，在 sess.run() 的时候通过 feed_dict 的方式，以键值对的方式传入参数
{% codeblock %}

import tensorflow as tf
# 使用 placeholder 的时候，要给类型
input1 = tf.placeholder(tf.float32)
# 规定结构 2 * 2
input2 = tf.placeholder(tf.float32,[2,2])
input3 = tf.placeholder(tf.float32)
# 下面是乘法运算
output = tf.multiply(input1,input3)
with tf.Session() as sess:
    # 因为我们之前用了 placeholder ，所以我们要调用参数 feed_dict 来传入参数 字典的形式
    print(sess.run(output,feed_dict={input1:[7.],input3:[8.]}))
        # [56.]

{% endcodeblock %}



# R
## reduce_mean()

	tf.reduce_mean(input_tensor, axis=None, keep_dims=False, name=None, reduction_indices=None)
	第一个参数input_tensor： 输入的待降维的tensor;
	第二个参数axis： 指定的轴，如果不指定，则计算所有元素的均值;
	第三个参数keep_dims：是否降维度，设置为True，输出的结果保持输入tensor的形状，设置为False，输出结果会降低维度;
	第四个参数name： 操作的名称;
	第五个参数 reduction_indices：在以前版本中用来指定轴，已弃用;
	
{% codeblock %}

import tensorflow as tf
 
x = [[1,2,3],
      [1,2,3]]
 
xx = tf.cast(x,tf.float32)
 
mean_all = tf.reduce_mean(xx, keep_dims=False)
mean_0 = tf.reduce_mean(xx, axis=0, keep_dims=False)
mean_1 = tf.reduce_mean(xx, axis=1, keep_dims=False)
 
 
with tf.Session() as sess:
    m_a,m_0,m_1 = sess.run([mean_all, mean_0, mean_1])
 
print m_a    # output: 2.0
print m_0    # output: [ 1.  2.  3.]
print m_1    #output:  [ 2.  2.]
# 如果设置保持原来的张量的维度，keep_dims=True ，结果：
print m_a    # output: [[ 2.]]
print m_0    # output: [[ 1.  2.  3.]]
print m_1    #output:  [[ 2.], [ 2.]]

{% endcodeblock %}
## reduce_sum()

	reduce_sum(
		input_tensor,
		axis=None,
		keep_dims=False,
		name=None,
		reduction_indices=None
	)
	
reduce_sum() 就是求和，由于求和的对象是tensor，所以是沿着tensor的某些维度求和。函数名中加了reduce是表示求和后会降维，当然可以通过设置参数来保证不降维，但是默认就是要降维的。
{% img /images/tensorflow/1_1.jpg %}


# S
## square
tf.square(x, name=None)
对x内的所有元素进行平方操作



# T
## trainable_variables()
tf.trainable_variables返回的是需要训练的变量列表
tf.all_variables返回的是所有变量的列表
{% codeblock %}

import tensorflow as tf;  
import numpy as np;  
import matplotlib.pyplot as plt;  
 
v = tf.Variable(tf.constant(0.0, shape=[1], dtype=tf.float32), name='v')
v1 = tf.Variable(tf.constant(5, shape=[1], dtype=tf.float32), name='v1')
global_step = tf.Variable(tf.constant(5, shape=[1], dtype=tf.float32), name='global_step', trainable=False)
ema = tf.train.ExponentialMovingAverage(0.99, global_step)
 
for ele1 in tf.trainable_variables():
	print ele1.name
for ele2 in tf.all_variables():
	print ele2.name

{% endcodeblock %}

	输出：
	v:0
	v1:0
	v:0
	v1:0
	global_step:0
	
分析：
上面得到两个变量，后面的一个得到上三个变量，因为global_step在声明的时候说明不是训练变量，用来关键字trainable=False。
# V
## Variable()
tensorflow 就好像 java 一样，变量必须声明之后才是变量，如果不声明就不是 tensorflow 变量。。。
{% codeblock %}

import tensorflow as tf
# 给变量赋予名字
state = tf.Variable(0,name='counter')
print(state.name)
    # counter:0
# 创建一个常数
one = tf.constant(1)
new_value = tf.add(state , one)
# 这个函数是将 new_value 加载到 state 上
update = tf.assign(state,new_value)

init = tf.initialize_all_variables()
with tf.Session() as sess:
    sess.run(init)
    for _ in range(3):
        sess.run(update)
        print(sess.run(state))
            # 1
            # 2
            # 3
			
{% endcodeblock %}
## variable_scope
[tensorflow: name_scope 和 variable_scope区别及理解](https://blog.csdn.net/u012609509/article/details/80045529)
[tensorflow里面name_scope, variable_scope等如何理解？](https://www.zhihu.com/question/54513728)
在训练深度网络时，为了减少需要训练参数的个数（比如具有simase结构的LSTM模型）、或是多机多卡并行化训练大数据大模型（比如数据并行化）等情况时，往往需要共享变量。另外一方面是当一个深度学习模型变得非常复杂的时候，往往存在大量的变量和操作，如何避免这些变量名和操作名的唯一不重复，同时维护一个条理清晰的graph非常重要。
之所以会出现这两种类型的scope，主要是后者（variable scope）为了实现tensorflow中的变量共享机制：即为了使得在代码的任何部分可以使用某一个已经创建的变量，TF引入了变量共享机制，使得可以轻松的共享变量，而不用传一个变量的引用。具体解释如下：
### tensorflow中创建variable的2种方式：
tf.Variable()：只要使用该函数，一律创建新的variable，如果出现重名，变量名后面会自动加上后缀1，2….
{% codeblock %}

import tensorflow as tf

with tf.name_scope('cltdevelop):
    var_1 = tf.Variable(initial_value=[0], name='var_1')
    var_2 = tf.Variable(initial_value=[0], name='var_1')
    var_3 = tf.Variable(initial_value=[0], name='var_1')
print(var_1.name)
print(var_2.name)
print(var_3.name)

	cltdevelop/var_1:0
	cltdevelop/var_1_1:0
	cltdevelop/var_1_2:0
			
{% endcodeblock %}
tf.get_variable()：如果变量存在，则使用以前创建的变量，如果不存在，则新创建一个变量。
### tensorflow中的两种作用域
命名域(name scope)：通过tf.name_scope()来实现；
变量域（variable scope）：通过tf.variable_scope()来实现；可以通过设置reuse 标志以及初始化方式来影响域下的变量。 
这两种作用域都会给tf.Variable()创建的变量加上词头，而tf.name_scope对tf.get_variable()创建的变量没有词头影响，代码如下：
{% codeblock %}

import tensorflow as tf

with tf.name_scope('cltdevelop'):
    var_1 = tf.Variable(initial_value=[0], name='var_1')
    var_2 = tf.get_variable(name='var_2', shape=[1, ])
with tf.variable_scope('aaa'):
    var_3 = tf.Variable(initial_value=[0], name='var_3')
    var_4 = tf.get_variable(name='var_4', shape=[1, ])

print(var_1.name)
print(var_2.name)
print(var_3.name)
print(var_4.name)

	cltdevelop/var_1:0
	var_2:0
	aaa/var_3:0
	aaa/var_4:0
			
{% endcodeblock %}
### tensorflow中变量共享机制的实现
在tensorflow中变量共享机制是通过tf.get_variable()和tf.variable_scope()两者搭配使用来实现的。如下代码所示：
{% codeblock %}

import tensorflow as tf

with tf.variable_scope('cltdevelop'):
    var_1 = tf.get_variable('var_1', shape=[1, ])
with tf.variable_scope('cltdevelop', reuse=True):
    var_2 = tf.get_variable('var_1', shape=[1,])

print(var_1.name)
print(var_2.name)
	
	cltdevelop/var_1:0
	cltdevelop/var_1:0

{% endcodeblock %}
[注：]当 reuse 设置为 True 或者 tf.AUTO_REUSE 时，表示这个scope下的变量是重用的或者共享的，也说明这个变量以前就已经创建好了。但如果这个变量以前没有被创建过，则在tf.variable_scope下调用tf.get_variable创建这个变量会报错。如下：
{% codeblock %}

import tensorflow as tf

with tf.variable_scope('cltdevelop', reuse=True):
    var_1 = tf.get_variable('var_1', shape=[1, ])

{% endcodeblock %}
则上述代码会报错

	ValueErrorL Variable cltdevelop/v1 doesnot exist, or was not created with tf.get_variable()
	
如果使用tf.get_variable()创建变量，且没有设置共享变量，重名时会报错
{% codeblock %}

import tensorflow as tf

with tf.name_scope('name_scope_1'):
    var1 = tf.get_variable(name='var1', shape=[1], dtype=tf.float32)
    var2 = tf.get_variable(name='var1', shape=[1], dtype=tf.float32)
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(var1.name, sess.run(var1))
    print(var2.name, sess.run(var2))

# ValueError: Variable var1 already exists, disallowed. Did you mean 
# to set reuse=True in VarScope? Originally defined at:
# var1 = tf.get_variable(name='var1', shape=[1], dtype=tf.float32)

{% endcodeblock %}
所以要共享变量，需要使用tf.variable_scope()
{% codeblock %}

import tensorflow as tf

with tf.variable_scope('variable_scope_y') as scope:
    var1 = tf.get_variable(name='var1', shape=[1], dtype=tf.float32)
    scope.reuse_variables()  # 设置共享变量
    var1_reuse = tf.get_variable(name='var1')
    var2 = tf.Variable(initial_value=[2.], name='var2', dtype=tf.float32)
    var2_reuse = tf.Variable(initial_value=[2.], name='var2', dtype=tf.float32)

with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    print(var1.name, sess.run(var1))
    print(var1_reuse.name, sess.run(var1_reuse))
    print(var2.name, sess.run(var2))
    print(var2_reuse.name, sess.run(var2_reuse))
# 输出结果：
# variable_scope_y/var1:0 [-1.59682846]
# variable_scope_y/var1:0 [-1.59682846]   可以看到变量var1_reuse重复使用了var1
# variable_scope_y/var2:0 [ 2.]
# variable_scope_y/var2_1:0 [ 2.]

{% endcodeblock %}
也可以这样
{% codeblock %}

with tf.variable_scope('foo') as foo_scope:
    v = tf.get_variable('v', [1])
with tf.variable_scope('foo', reuse=True):
    v1 = tf.get_variable('v')
assert v1 == v

{% endcodeblock %}
或者这样：
{% codeblock %}

with tf.variable_scope('foo') as foo_scope:
    v = tf.get_variable('v', [1])
with tf.variable_scope(foo_scope, reuse=True):
    v1 = tf.get_variable('v')
assert v1 == v

{% endcodeblock %}




















