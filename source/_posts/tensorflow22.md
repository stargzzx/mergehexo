---
title: tensorflow 的变量以及初始化
date: 2019-03-25 10:37:35
categories:
- tensorflow
tags:
- 变量
- tensorflow
---
我们主要是讲解两个内容，一个是声明变量的两种形式，另一个是变量的初始化，也就是 sess.run(init) 中的 init。
<!-- more -->
## 参考资料
[tf.get_variable()和tf.Variable()的区别](https://blog.csdn.net/qq_33915826/article/details/79793171)
[共享变量](http://wiki.jikexueyuan.com/project/tensorflow-zh/how_tos/variable_scope.html)
[Tensorflow中的变量初始化函数](https://blog.csdn.net/zSean/article/details/75091509)
[tensorflow中的参数初始化方法](https://blog.csdn.net/dcrmg/article/details/80034075)
[Tensorflow学习笔记: 变量及共享变量](https://zhuanlan.zhihu.com/p/37922147)
## 变量的声明
TensorFlow中定义变量的时候有两种操作。
tf.get_variable()和tf.Variable()。
TensorFlow中变量主要用来表示机器学习模型中的参数，变量通过 tf.Variable 类进行操作。tf.Variable 表示张量，通过运行 op 可以改变它的值。与 tf.Tensor 对象不同，tf.Variable 存在于单个 session.run 调用的上下文之外。
在内部，tf.Variable 存储持久张量。具体 op 允许您读取和修改此张量的值。这些修改在多个 tf.Session 之间是可见的，因此对于一个 tf.Variable，多个工作器可以看到相同的值。
### tf.Variable 创建变量
tf.Variable的初始化函数如下所示

	__init__(
		initial_value=None,
		trainable=True,
		collections=None,
		validate_shape=True,
		caching_device=None,
		name=None,
		variable_def=None,
		dtype=None,
		expected_shape=None,
		import_scope=None,
		constraint=None
	)

其中参数

	initial_value 表示初始化值，用Tensor表示
	trainable 表示变量是否被训练，如果被训练，将加入到tf.GraphKeys.TRAINABLE_VARIABLES集合中，TensorFlow将计算其梯度的变量
	collections 表示一个graph collections keys的集合，这个创建的变量将被添加到这些集合中，默认集合是[GraphKeys.GLOBAL_VARIABLES].
	name: 变量的命名，默认是'Variable'
	dtype 表示类型

例如我们创建一个变量，并且查看其name和shape
{% codeblock %}

import tensorflow as tf
w1 = tf.Variable(tf.random_normal([784,200], stddev = 0.35), name="weights")
b1 = tf.Variable(tf.zeros([200]),name="biases")

w2 = tf.Variable(tf.random_normal([784,200], stddev = 0.35), name="weights") # 名称相同
b2 = tf.Variable(tf.zeros([200]),name="biases") # 名称相同

print w1.name, w1.shape
print b1.name, b1.shape
print w2.name, w2.shape
print b2.name, b2.shape

************************************输出**************************************
weights:0 (784, 200)
biases:0 (200,)
weights_1:0 (784, 200)
biases_1:0 (200,)
	
{% endcodeblock %}
可以看到在命名的时候，如果指定的name重复，那么w2就会被命名为"name_1:0" 这样累加下去。
### 变量集合 collections
默认情况下，每个tf.Variable都放置在以下两个集合中：*tf.GraphKeys.GLOBAL_VARIABLES- 可以在多个设备共享的变量，*tf.GraphKeys.TRAINABLE_VARIABLES- TensorFlow 将计算其梯度的变量。
#### 查看集合变量列表
要查看放置在某个集合中的所有变量的列表，可以采用如下方式
{% codeblock %}

import tensorflow as tf
w1 = tf.Variable(tf.random_normal([784,200], stddev = 0.35), name="weights")
b1 = tf.Variable(tf.zeros([200]),name="biases")

w2 = tf.Variable(tf.random_normal([784,200], stddev = 0.35), name="weights") # 名称相同
b2 = tf.Variable(tf.zeros([200]),name="biases") # 名称相同

print tf.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)

************************************输出**************************************
[<tf.Variable 'weights:0' shape=(784, 200) dtype=float32_ref>, <tf.Variable 'biases:0' shape=(200,) dtype=float32_ref>, <tf.Variable 'weights_1:0' shape=(784, 200) dtype=float32_ref>, <tf.Variable 'biases_1:0' shape=(200,) dtype=float32_ref>]
	
{% endcodeblock %}
可以看到输出结果是所有变量的列表
#### 创建变量集合
如果您不希望变量被训练，可以将其添加到 tf.GraphKeys.LOCAL_VARIABLES 集合中。例如，以下代码段展示了如何将名为 my_local 的变量添加到此集合中：

	my_local = tf.get_variable("my_local", shape=(),
	collections=[tf.GraphKeys.LOCAL_VARIABLES])
	
或者，您可以指定 trainable=False 为 tf.get_variable 的参数：

	my_non_trainable = tf.get_variable("my_non_trainable",
									   shape=(),
									   trainable=False)
									
我们测试效果如下所示，可以看到b2的trainable=False，那么输出collection没有b2
{% codeblock %}

import tensorflow as tf
w1 = tf.Variable(tf.random_normal([784,200], stddev = 0.35), name="weights")
b1 = tf.Variable(tf.zeros([200]),name="biases")

w2 = tf.Variable(tf.random_normal([784,200], stddev = 0.35), name="weights") # 名称相同
b2 = tf.Variable(tf.zeros([200]),name="biases", trainable=False) # 名称相同

print tf.get_collection(tf.GraphKeys.TRAINABLE_VARIABLES)

************************************输出**************************************
[<tf.Variable 'weights:0' shape=(784, 200) dtype=float32_ref>, <tf.Variable 'biases:0' shape=(200,) dtype=float32_ref>, <tf.Variable 'weights_1:0' shape=(784, 200) dtype=float32_ref>]

{% endcodeblock %}
您也可以使用自己的集合。集合名称可为任何字符串，且您无需显式创建集合。创建变量（或任何其他对象）后，要将其添加到集合，请调用 tf.add_to_collection。例如，以下代码将名为 my_local 的现有变量添加到名为 my_collection_name 的集合中：

tf.add_to_collection("my_collection_name", my_local)

### 共享变量
我们查看下面的代码，表示一个卷积神经网络，其中包括conv1_weights, conv1_biases, conv2_weights, conv2_biases四个参数，也就是4个变量
{% codeblock %}

def my_image_filter(input_images):
    conv1_weights = tf.Variable(tf.random_normal([5, 5, 32, 32]),
        name="conv1_weights")
    conv1_biases = tf.Variable(tf.zeros([32]), name="conv1_biases")
    conv1 = tf.nn.conv2d(input_images, conv1_weights,
        strides=[1, 1, 1, 1], padding='SAME')
    relu1 = tf.nn.relu(conv1 + conv1_biases)

    conv2_weights = tf.Variable(tf.random_normal([5, 5, 32, 32]),
        name="conv2_weights")
    conv2_biases = tf.Variable(tf.zeros([32]), name="conv2_biases")
    conv2 = tf.nn.conv2d(relu1, conv2_weights,
        strides=[1, 1, 1, 1], padding='SAME')
    return tf.nn.relu(conv2 + conv2_biases)
	
{% endcodeblock %}
假设我们利用这个函数对两张图片进行相同的操作，也就是调用两次，那么每次都会创建4个变量，假设我们在函数内对变量进行了优化求解，那么每次都会重新创建变量，这样就无法复用参数，导致训练过程无效
{% codeblock %}

# 第一次执行方法创建4个变量
result1 = my_image_filter(image1)
# 第二次执行再创建4个变量
result2 = my_image_filter(image2)

ValueError: Variable weight already exists, disallowed. Did you mean to set reuse=True or reuse=tf.AUTO_REUSE in VarScope? Originally defined at:
	
{% endcodeblock %}
TensowFlow通过变量范围(variable scope)和tf.get_variable方法解决了共享变量(参数)的问题。
#### tf.variable_scope和tf.get_variable
tf.Variable()方法每次被调用都会创建新的变量，这样就无法解决共享变量的问题，而tf.get_variable结合作用域即可表明我们是想创建新的变量，还是共享变量，变量作用域允许在调用隐式创建和使用变量的函数时控制变量重用。作用域还允许您以分层和可理解的方式命名变量。tf.get_variable()的机制跟tf.Variable()有很大不同，如果指定的变量名已经存在（即先前已经用同一个变量名通过get_variable()函数实例化了变量），那么get_variable()只会返回之前的变量，否则才创造新的变量。我们举例进行说明。
例如上面的例子中有两个卷积层，我们先来编写一个函数创建一个卷积/relu层，这个函数使命的变量名称是'weights'和'biases'

{% codeblock %}

def conv_relu(input, kernel_shape, bias_shape):
    # Create variable named "weights".
    weights = tf.get_variable("weights", kernel_shape,
        initializer=tf.random_normal_initializer())
    # Create variable named "biases".
    biases = tf.get_variable("biases", bias_shape,
        initializer=tf.constant_initializer(0.0))
    conv = tf.nn.conv2d(input, weights,
        strides=[1, 1, 1, 1], padding='SAME')
    return tf.nn.relu(conv + biases)
	
{% endcodeblock %}
在真实模型中需要多个卷积层，我们通过变量域来区分不同层的变量，不同的变量域下的变量名车为：scope_name/variable_name, 如下所示，第一个卷积层的变量名称是'conv1/weights', 'conv1/biases', 第二个卷积层的变量名称是 'conv2/weights', 'conv2/biases'。
{% codeblock %}

def my_image_filter(input_images):
    with tf.variable_scope("conv1"):
        # Variables created here will be named "conv1/weights", "conv1/biases".
        relu1 = conv_relu(input_images, [5, 5, 32, 32], [32])
    with tf.variable_scope("conv2"):
        # Variables created here will be named "conv2/weights", "conv2/biases".
        return conv_relu(relu1, [5, 5, 32, 32], [32])
	
{% endcodeblock %}
但即便这样，如果多次调用该函数，也会抛出异常，

	result1 = my_image_filter(image1)
	result2 = my_image_filter(image2)
	# Raises ValueError(... conv1/weights already exists ...)

因为用get_variable()创建两个相同名字的变量是会报错的，默认上它只是检查变量名，防止重复，如果要变量共享，就需要指定在哪个域名内可以共享变量。
如下面的代码。
{% codeblock %}

import tensorflow as tf


def test(b):
    a = tf.get_variable(name='test',shape=[2,2],initializer=tf.truncated_normal_initializer(stddev=0.02))
    a = tf.matmul(b,a)
    return a

with tf.variable_scope('test') as scope:
    b = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    d = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    c = test(b)
    scope.reuse_variables()
    d = test(d)

with tf.Session() as sess:
    init = tf.global_variables_initializer()
    sess.run(init)
    print(sess.run(c))
    print(sess.run(d))
	
	# 上面两个输出是一样的
	
{% endcodeblock %}
开启共享变量有两种方式
##### 方法1
采用scope.reuse_variables()触发重用变量，如下所示
{% codeblock %}

with tf.variable_scope("model") as scope:
  output1 = my_image_filter(input1)
  scope.reuse_variables()
  output2 = my_image_filter(input2)
	
{% endcodeblock %}
##### 方法2
{% codeblock %}

with tf.variable_scope("model"):
  output1 = my_image_filter(input1)
with tf.variable_scope("model", reuse=True):
  output2 = my_image_filter(input2)
	
{% endcodeblock %}
##### 几个简单的例子
{% codeblock %}

import tensorflow as tf
def test(b):
    a = tf.get_variable(name='test',shape=[2,2],initializer=tf.truncated_normal_initializer(stddev=0.02))
    a = tf.matmul(b,a)
    return a

with tf.variable_scope('test'):
    b = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    c = test(b)

with tf.variable_scope('test1'):
    d = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    d = test(d)

with tf.Session() as sess:
    init = tf.global_variables_initializer()
    sess.run(init)
    print(sess.run(c))
    print(sess.run(d))
	
	# [[-0.01371786 -0.02131779]
	# [-0.02743572 -0.04263557]]
	#[[0.02182615 0.01196657]
	# [0.04365229 0.02393314]]
	
{% endcodeblock %}
{% codeblock %}

import tensorflow as tf
def test(b):
    a = tf.get_variable(name='test',shape=[2,2],initializer=tf.truncated_normal_initializer(stddev=0.02))
    a = tf.matmul(b,a)
    return a

with tf.variable_scope('test'):
    b = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    c = test(b)

with tf.variable_scope('test',reuse=True):
    d = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    d = test(d)


with tf.Session() as sess:
    init = tf.global_variables_initializer()
    sess.run(init)
    print(sess.run(c))
    print(sess.run(d))
	# [[-0.02963482 -0.01020483]
	# [-0.05926965 -0.02040966]]
	# [[-0.02963482 -0.01020483]
	# [-0.05926965 -0.02040966]]
	# 两个输出是相同的
	
{% endcodeblock %}
{% codeblock %}

import tensorflow as tf
def test(b):
    a = tf.get_variable(name='test',shape=[2,2],initializer=tf.truncated_normal_initializer(stddev=0.02))
    a = tf.matmul(b,a)
    return a

with tf.variable_scope('test'):
    b = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    c = test(b)

with tf.variable_scope('test',reuse=False):
    d = tf.constant([[1.0, 1.0], [2.0, 2.0]])
    d = test(d)


with tf.Session() as sess:
    init = tf.global_variables_initializer()
    sess.run(init)
    print(sess.run(c))
    print(sess.run(d))
	# 这个会出错
	# ValueError: Variable test/test already exists, disallowed. Did you mean to set reuse=True or reuse=tf.AUTO_REUSE in VarScope?
	
{% endcodeblock %}
#### 理解variable_scope
理解变量域的工作机理非常重要，我们对其进行梳理，当我们调用tf.get_variable(name, shape, dtype, initializer)时，这背后到底做了什么
首先，TensorFlow 会判断是否要共享变量，也就是判断 tf.get_variable_scope().reuse 的值，如果结果为 False（即你没有在变量域内调用scope.reuse_variables()），那么 TensorFlow 认为你是要初始化一个新的变量，紧接着它会判断这个命名的变量是否存在。如果存在，会抛出 ValueError 异常，否则，就根据 initializer 初始化变量：
{% codeblock %}

with tf.variable_scope("foo"):
    v = tf.get_variable("v", [1])
assert v.name == "foo/v:0"
	
{% endcodeblock %}
而如果 tf.get_variable_scope().reuse == True，那么 TensorFlow 会执行相反的动作，就是到程序里面寻找变量名为 scope name + name 的变量，如果变量不存在，会抛出 ValueError 异常，否则，就返回找到的变量：
{% codeblock %}

with tf.variable_scope("foo"):
    v = tf.get_variable("v", [1])
with tf.variable_scope("foo", reuse=True):
    v1 = tf.get_variable("v", [1])
assert v1 is v
	
{% endcodeblock %}
变量域可以多层重叠，例如，下面的变量上有两层的变量域，那么变量名是'foo/var/v:0'
{% codeblock %}

with tf.variable_scope("foo"):
    with tf.variable_scope("bar"):
        v = tf.get_variable("v", [1])
assert v.name == "foo/bar/v:0"
	
{% endcodeblock %}
在同一个变量域中，如果需要调用同名变量，那么需要重用变量即可，例如v1和v两个变量时相同的，因为变量名都是'foo/v'
{% codeblock %}

with tf.variable_scope("foo"):
    v = tf.get_variable("v", [1])
    tf.get_variable_scope().reuse_variables()
    v1 = tf.get_variable("v", [1])
assert v1 is v
	
{% endcodeblock %}

### 总结
tf.get_variable()默认上它只检查变量名，如果变量名重复，那么就会报错；tf.Variable()每次被调用都创建相应的变量，即便变量名重复，也会创建新的变量，因此无法共享变量名。
如果scope中开启共享变量，那么调用tf.get_variable()就会查找相同变量名的变量，如果有，就直接返回该变量，如果没有，就创建一个新的变量；
如果scope没有开启共享变量(默认模式)，那么 调用tf.get_variable()发现已有相同变量名的变量，就会报错，如果没有，就创建一个新的变量。
要重用变量，需要在scope中开启共享变量，有两种方法，推荐第一种
### 区别
使用tf.Variable时，如果检测到命名冲突，系统会自己处理。使用tf.get_variable()时，系统不会处理冲突，而会报错。
tf.Variable()每次都在创建新的对象，与name没有关系。而tf.get_variable()对于已经创建的同样name的变量对象，就直接把那个变量对象返回（类似于：共享变量），tf.get_variable() 会检查当前命名空间下是否存在同样name的变量，可以方便共享变量。
tf.get_variable()：对于在上下文管理器中已经生成一个v的变量，若想通过tf.get_variable函数获取其变量，则可以通过reuse参数的设定为True来获取。
还有一点，tf.get_variable()必须写name，否则报错（but instead was %s." % (name, shape)) 
ValueError: Shape of a new variable (Tensor("truncated_normal:0", shape=(2, 3), dtype=float32)) must be fully defined, but instead was <unknown>.），tf.Variable()不要求。

## 多种方式的初始化
### tf.constant_initializer(value)
初始化为常量
tf中使用tf.constant_initializer(value)类生成一个初始值为常量value的tensor对象。
constant_initializer类的构造函数定义：
{% codeblock %}

def __init__(self, value=0, dtype=dtypes.float32, verify_shape=False):
    self.value = value
    self.dtype = dtypes.as_dtype(dtype)
    self._verify_shape = verify_shape

	# value：指定的常量
	# dtype： 数据类型
	# verify_shape： 是否可以调整tensor的形状，默认可以调整
	
{% endcodeblock %}
{% codeblock %}

import tensorflow as tf
value = [0, 1, 2, 3, 4, 5, 6, 7]
init = tf.constant_initializer(value)
with tf.Session() as sess:
  x = tf.get_variable('x', shape=[8], initializer=init)
  x.initializer.run()
  print(x.eval())
#output:
#[ 0.  1.  2.  3.  4.  5.  6.  7.]
	
{% endcodeblock %}
### tf.zeros_initializer() tf.ones_initializer()
可以简写为tf.Zeros()或者可简写为tf.Ones()
神经网络中经常使用常量初始化方法来初始化偏置项。
当初始化一个维数很多的常量时，一个一个指定每个维数上的值很不方便，tf提供了 tf.zeros_initializer() 和 tf.ones_initializer() 类，分别用来初始化全0和全1的tensor对象
{% codeblock %}

import tensorflow as tf
init_zeros=tf.zeros_initializer()
init_ones = tf.ones_initializer
with tf.Session() as sess:
  x = tf.get_variable('x', shape=[8], initializer=init_zeros)
  y = tf.get_variable('y', shape=[8], initializer=init_ones)
  x.initializer.run()
  y.initializer.run()
  print(x.eval())
  print(y.eval())
#output:
# [ 0.  0.  0.  0.  0.  0.  0.  0.]
# [ 1.  1.  1.  1.  1.  1.  1.  1.]
	
{% endcodeblock %}
### tf.random_normal_initializer(mean,stddev) tf.truncated_normal_initializer() 
功能是将变量初始化为满足正太分布的随机值，主要参数（正太分布的均值和标准差），用所给的均值和标准差初始化均匀分布
初始化参数为正太分布在神经网络中应用的最多，可以初始化为标准正太分布和截断正太分布。
tf中使用 tf.random_normal_initializer() 类来生成一组符合标准正太分布的tensor。
tf中使用 tf.truncated_normal_initializer() 类来生成一组符合截断正太分布的tensor。
tf.random_normal_initializer 类和 tf.truncated_normal_initializer 的构造函数定义：
{% codeblock %}

def __init__(self, mean=0.0, stddev=1.0, seed=None, dtype=dtypes.float32):
    self.mean = mean
    self.stddev = stddev
    self.seed = seed
    self.dtype = _assert_float_dtype(dtypes.as_dtype(dtype))
	
	# mean： 正太分布的均值，默认值0
	# stddev： 正太分布的标准差，默认值1
	# seed： 随机数种子，指定seed的值可以每次都生成同样的数据
	# dtype： 数据类型
	
{% endcodeblock %}
{% codeblock %}

import tensorflow as tf
init_random = tf.random_normal_initializer(mean=0.0, stddev=1.0, seed=None, dtype=tf.float32)
init_truncated = tf.truncated_normal_initializer(mean=0.0, stddev=1.0, seed=None, dtype=tf.float32)
with tf.Session() as sess:
  x = tf.get_variable('x', shape=[10], initializer=init_random)
  y = tf.get_variable('y', shape=[10], initializer=init_truncated)
  x.initializer.run()
  y.initializer.run()
  print(x.eval())
  print(y.eval())
 
#output:
# [-0.40236568 -0.35864913 -0.94253045 -0.40153521  0.1552504   1.16989613
#   0.43091929 -0.31410623  0.70080078 -0.9620409 ]
# [ 0.18356581 -0.06860946 -0.55245203  1.08850253 -1.13627422 -0.1006074
#   0.65564936  0.03948414  0.86558545 -0.4964745 ]

{% endcodeblock %}
### tf.random_uniform_initializer(a,b,seed,dtype)
初始化为均匀分布,从a到b均匀初始化，将变量初始化为满足平均分布的随机值，主要参数（最大值，最小值）
{% codeblock %}

def __init__(self, minval=0, maxval=None, seed=None, dtype=dtypes.float32):
    self.minval = minval
    self.maxval = maxval
    self.seed = seed
    self.dtype = dtypes.as_dtype(dtype)
	
	# minval: 最小值
	# maxval： 最大值
	# seed：随机数种子
	# dtype： 数据类型


{% endcodeblock %}
{% codeblock %}

import tensorflow as tf
init_uniform = tf.random_uniform_initializer(minval=0, maxval=10, seed=None, dtype=tf.float32)
with tf.Session() as sess:
  x = tf.get_variable('x', shape=[10], initializer=init_uniform)
  x.initializer.run()
  print(x.eval())
  
# output:
# [ 6.93343639  9.41196823  5.54009819  1.38017178  1.78720832  5.38881063
#   3.39674473  8.12443542  0.62157512  8.36026382]

{% endcodeblock %}
从输出可以看到，均匀分布生成的随机数并不是从小到大或者从大到小均匀分布的，这里均匀分布的意义是每次从一组服从均匀分布的数里边随机抽取一个数。
### tf.uniform_unit_scaling_initializer(factor,seed,dtypr)
将变量初始化为满足平均分布但不影响输出数量级的随机值
{% codeblock %}

def __init__(self, factor=1.0, seed=None, dtype=dtypes.float32):
    self.factor = factor
    self.seed = seed
    self.dtype = _assert_float_dtype(dtypes.as_dtype(dtype))

{% endcodeblock %}
同样都是生成均匀分布，tf.uniform_unit_scaling_initializer 跟 tf.random_uniform_initializer 不同的地方是前者不需要指定最大最小值，是通过公式计算出来的：

	max_val = math.sqrt(3 / input_size) * factor
	min_val = -max_val

input_size是生成数据的维度，factor是系数。
{% codeblock %}

import tensorflow as tf
init_uniform_unit = tf.uniform_unit_scaling_initializer(factor=1.0, seed=None, dtype=tf.float32)
with tf.Session() as sess:
  x = tf.get_variable('x', shape=[10], initializer=init_uniform_unit)
  x.initializer.run()
  print(x.eval())
 
# output:
# [-1.65964031  0.59797513 -0.97036457 -0.68957627  1.69274557  1.2614969
#   1.55491126  0.12639415  0.54466736 -1.56159735]

{% endcodeblock %}
### 初始化为变尺度正太、均匀分布
tf中tf.variance_scaling_initializer()类可以生成截断正太分布和均匀分布的tensor，增加了更多的控制参数。构造函数：
{% codeblock %}

def __init__(self, scale=1.0,
               mode="fan_in",
               distribution="normal",
               seed=None,
               dtype=dtypes.float32):
    if scale <= 0.:
      raise ValueError("`scale` must be positive float.")
    if mode not in {"fan_in", "fan_out", "fan_avg"}:
      raise ValueError("Invalid `mode` argument:", mode)
    distribution = distribution.lower()
    if distribution not in {"normal", "uniform"}:
      raise ValueError("Invalid `distribution` argument:", distribution)
    self.scale = scale
    self.mode = mode
    self.distribution = distribution
    self.seed = seed
    self.dtype = _assert_float_dtype(dtypes.as_dtype(dtype))

		# scale: 缩放尺度
		# mode： 有3个值可选，分别是 “fan_in”, “fan_out” 和 “fan_avg”，用于控制计算标准差 stddev的值
		# distribution： 2个值可选，”normal”或“uniform”，定义生成的tensor的分布是截断正太分布还是均匀分布

{% endcodeblock %}
distribution选‘normal’的时候，生成的是截断正太分布，标准差 stddev = sqrt(scale / n), n的取值根据mode的不同设置而不同：

	mode = "fan_in"， n为输入单元的结点数；         
	mode = "fan_out"，n为输出单元的结点数；
	mode = "fan_avg",n为输入和输出单元结点数的平均值;
	
distribution选 ‘uniform’，生成均匀分布的随机数tensor，最大值 max_value和 最小值 min_value 的计算公式：

	max_value = sqrt(3 * scale / n)
	min_value = -max_value
	
{% codeblock %}

import tensorflow as tf
init_variance_scaling_normal = tf.variance_scaling_initializer(scale=1.0,mode="fan_in",
                                                        distribution="normal",seed=None,dtype=tf.float32)
init_variance_scaling_uniform = tf.variance_scaling_initializer(scale=1.0,mode="fan_in",
                                                        distribution="uniform",seed=None,dtype=tf.float32)
with tf.Session() as sess:
  x = tf.get_variable('x', shape=[10], initializer=init_variance_scaling_normal)
  y = tf.get_variable('y', shape=[10], initializer=init_variance_scaling_uniform)
  x.initializer.run()
  y.initializer.run()
  print(x.eval())
  print(y.eval())
# output:
# [ 0.55602223  0.36556259  0.39404872 -0.11241052  0.42891756 -0.22287074
#   0.15629818  0.56271428 -0.15364751 -0.03651841]
# [ 0.22965753 -0.1339919  -0.21013224  0.112804   -0.49030468  0.21375734
#   0.24524075 -0.48397955  0.02254289 -0.07996771]

{% endcodeblock %}
### 其他初始化方式

	tf.orthogonal_initializer() 初始化为正交矩阵的随机数，形状最少需要是二维的
	tf.glorot_uniform_initializer() 初始化为与输入输出节点数相关的均匀分布随机数
	tf.glorot_normal_initializer（） 初始化为与输入输出节点数相关的截断正太分布随机数

{% codeblock %}

import tensorflow as tf
init_orthogonal = tf.orthogonal_initializer(gain=1.0, seed=None, dtype=tf.float32)
init_glorot_uniform = tf.glorot_uniform_initializer()
init_glorot_normal = tf.glorot_normal_initializer()
with tf.Session() as sess:
  x = tf.get_variable('x', shape=[4,4], initializer=init_orthogonal)
  y = tf.get_variable('y', shape=[10], initializer=init_glorot_uniform)
  z = tf.get_variable('z', shape=[10], initializer=init_glorot_normal)
  x.initializer.run()
  y.initializer.run()
  z.initializer.run()
  print(x.eval())
  print(y.eval())
  print(z.eval())
 
# output:
# [[ 0.41819954  0.38149482  0.82090431  0.07541249]
#  [ 0.41401231  0.21400851 -0.38360971  0.79726893]
#  [ 0.73776144 -0.62585676 -0.06246936 -0.24517137]
#  [ 0.33077344  0.64572859 -0.41839844 -0.54641217]]
# [-0.11182356  0.01995623 -0.0083192  -0.09200105  0.49967837  0.17083591
#   0.37086374  0.09727859  0.51015782 -0.43838671]
# [-0.50223351  0.18181904  0.43594137  0.3390047   0.61405027  0.02597036
#   0.31719241  0.04096413  0.10962497 -0.13165198]

{% endcodeblock %}
### 全局初始化
在使用Tensorflow框架进行两层卷积神经网络实现的时候，使用tf.global_Variables_initializer()进行初始化时一定要加.run()方法，Tensorflow与我们正常的编程思想略有不同：Tensorflow中的语句不会立即执行，而是等到开启会话Session的时候，才会执行session.run()的语句；其次是版本的问题，tf.global_Variables_initializer().run()是最新版本的全局初使化语句，较旧版本的写法为：tf.initilize_all_variables().run().








