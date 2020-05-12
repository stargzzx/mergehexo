---
title: 模型的保存和提取
date: 2019-03-25 23:22:54
categories:
- [人工智能,深度学习,框架,tensorflow]
tags:
- tensorflow
- 模型保存
---
内容如题。

<!-- more -->

## 参考资料

[TensorFlow模型保存和提取方法](https://blog.csdn.net/marsjhao/article/details/72829635)

[Tensorflow模型的保存与恢复](https://www.cnblogs.com/hejunlin1992/p/7767912.html)

## 目录

在这篇tensorflow教程中，我会解释：

Tensorflow的模型（model）长什么样子？

如何保存tensorflow的模型？

如何恢复一个tensorflow模型来用于预测或者迁移学习？

如何使用预训练好的模型（imported pretrained models）来用于fine-tuning和 modification

## Tensorflow模型是什么？

当你已经训练好一个神经网络之后，你想要保存它，用于以后的使用，部署到产品里面去。所以，Tensorflow模型是什么？Tensorflow模型主要包含网络的设计或者图（graph），和我们已经训练好的网络参数的值。因此Tensorflow模型有两个主要的文件：

### Meta graph:

这是一个保存完整Tensorflow graph的protocol buffer，比如说，所有的 variables, operations, collections等等。这个文件的后缀是 .meta 。

### Checkpoint file:

这是一个包含所有权重（weights），偏置（biases），梯度（gradients）和所有其他保存的变量（variables）的二进制文件。它包含两个文件：

	mymodel.data-00000-of-00001
	mymodel.index

其中，.data文件包含了我们的训练变量。

另外，除了这两个文件，Tensorflow有一个叫做checkpoint的文件，记录着已经最新的保存的模型文件。

注：Tensorflow 0.11版本以前，Checkpoint file只有一个后缀名为.ckpt的文件。

因此，总结来说，Tensorflow（版本0.10以后）模型长这个样子：

![](/images/tensorflow/23_0.png)

Tensorflow版本0.11以前，只包含以下三个文件：

	inception_v1.meta
	inception_v1.ckpt
	checkpoint

## 保存一个Tensorflow模型

当网络训练结束时，我们要保存所有变量和网络结构体到文件中。在Tensorflow中，我们可以创建一个tf.train.Saver() 类的实例，如下：

	saver = tf.train.Saver()
	
由于Tensorflow变量仅仅只在session中存在，因此需要调用save方法来将模型保存在一个session中。

	saver.save(sess, 'my-test-model')
	
在这里，sess是一个session对象，其中my-test-model是你给模型起的名字。下面是一个完整的例子：

```python
import tensorflow as tf
w1 = tf.Variable(tf.random_normal(shape=[2]), name='w1')
w2 = tf.Variable(tf.random_normal(shape=[5]), name='w2')
saver = tf.train.Saver()
sess = tf.Session()
sess.run(tf.global_variables_initializer())
saver.save(sess, 'my_test_model')

# This will save following files in Tensorflow v >= 0.11
# my_test_model.data-00000-of-00001
# my_test_model.index
# my_test_model.meta
# checkpoint
```

如果我们想在训练1000次迭代之后保存模型，可以使用如下方法保存

	saver.save(sess, 'my_test_model',global_step=1000)
	
这个将会在模型名字的后面追加上‘-1000’，下面的文件将会被创建：

	my_test_model-1000.index
	my_test_model-1000.meta
	my_test_model-1000.data-00000-of-00001
	checkpoint

由于网络的图（graph）在训练的时候是不会改变的，因此，我们没有必要每次都重复保存.meta文件，可以使用如下方法：

	saver.save(sess, 'my-model',global_step=step,write_meta_graph=False)
	
如果你只想要保存最新的4个模型，并且想要在训练的时候每2个小时保存一个模型，那么你可以使用max_to_keep 和 keep_checkpoint_every_n_hours，如下所示：

	#saves a model every 2 hours and maximum 4 latest models are saved.
	saver = tf.train.Saver(max_to_keep=4, keep_checkpoint_every_n_hours=2)

注意到，我们在tf.train.Saver()中并没有指定任何东西，因此它将保存所有变量。如果我们不想保存所有的变量，只想保存其中一些变量，我们可以在创建tf.train.Saver实例的时候，给它传递一个我们想要保存的变量的list或者字典。示例如下：

```python
import tensorflow as tf
w1 = tf.Variable(tf.random_normal(shape=[2]), name='w1')
w2 = tf.Variable(tf.random_normal(shape=[5]), name='w2')
saver = tf.train.Saver([w1,w2])
sess = tf.Session()
sess.run(tf.global_variables_initializer())
saver.save(sess, 'my_test_model',global_step=1000)
```

## 导入一个已经训练好的模型

如果你想要使用别人已经训练好的模型来fine-tuning，那么你需要做两个步骤：

### 创建网络Create the network:

你可以通过写python代码，来手动地创建每一个、每一层，使得跟原始网络一样。

但是，如果你仔细想的话，我们已经将模型保存在了 .meta 文件中，因此我们可以使用tf.train.import()函数来重新创建网络，使用方法如下：

	saver = tf.train.import_meta_graph('my_test_model-1000.meta')
	
注意，这仅仅是将已经定义的网络导入到当前的graph中，但是我们还是需要加载网络的参数值。

### 加载参数Load the parameters

我们可以通过调用restore函数来恢复网络的参数，如下：

	with tf.Session() as sess:
	  new_saver = tf.train.import_meta_graph('my_test_model-1000.meta')
	  new_saver.restore(sess, tf.train.latest_checkpoint('./'))
	  
在这之后，像w1和w2的tensor的值已经被恢复，并且可以获取到：

	with tf.Session() as sess:    
		saver = tf.train.import_meta_graph('my-model-1000.meta')
		saver.restore(sess,tf.train.latest_checkpoint('./'))
		print(sess.run('w1:0'))
	##Model has been restored. Above statement will print the saved value of w1.
	
上面介绍了如何保存和恢复一个Tensorflow模型。下面介绍一个加载任何预训练模型的实用方法。

## Working with restored models

下面介绍如何恢复任何一个预训练好的模型，并使用它来预测，fine-tuning或者进一步训练。当你使用Tensorflow时，你会定义一个图（graph），其中，你会给这个图喂（feed）训练数据和一些超参数（比如说learning rate，global step等）。下面我们使用placeholder建立一个小的网络，然后保存该网络。注意到，当网络被保存时，placeholder的值并不会被保存。

```python
import tensorflow as tf

#Prepare to feed input, i.e. feed_dict and placeholders
w1 = tf.placeholder("float", name="w1")
w2 = tf.placeholder("float", name="w2")
b1= tf.Variable(2.0,name="bias")
feed_dict ={w1:4,w2:8}

#Define a test operation that we will restore
w3 = tf.add(w1,w2)
w4 = tf.multiply(w3,b1,name="op_to_restore")
sess = tf.Session()
sess.run(tf.global_variables_initializer())

#Create a saver object which will save all the variables
saver = tf.train.Saver()

#Run the operation by feeding input
print sess.run(w4,feed_dict)
#Prints 24 which is sum of (w1+w2)*b1 

#Now, save the graph
saver.save(sess, 'my_test_model',global_step=1000)		
```

现在，我们想要恢复这个网络，我们不仅需要恢复图（graph）和权重，而且也需要准备一个新的feed_dict，将新的训练数据喂给网络。我们可以通过使用graph.get_tensor_by_name()方法来获得已经保存的操作（operations）和placeholder variables。
	
	#How to access saved variable/Tensor/placeholders 
	w1 = graph.get_tensor_by_name("w1:0")
	## How to access saved operation
	op_to_restore = graph.get_tensor_by_name("op_to_restore:0")

如果我们仅仅想要用不同的数据运行这个网络，可以简单的使用feed_dict来将新的数据传递给网络。

```python
import tensorflow as tf

sess=tf.Session()    
#First let's load meta graph and restore weights
saver = tf.train.import_meta_graph('my_test_model-1000.meta')
saver.restore(sess,tf.train.latest_checkpoint('./'))


# Now, let's access and create placeholders variables and
# create feed-dict to feed new data

graph = tf.get_default_graph()
w1 = graph.get_tensor_by_name("w1:0")
w2 = graph.get_tensor_by_name("w2:0")
feed_dict ={w1:13.0,w2:17.0}

#Now, access the op that you want to run. 
op_to_restore = graph.get_tensor_by_name("op_to_restore:0")

print sess.run(op_to_restore,feed_dict)
#This will print 60 which is calculated 
#using new values of w1 and w2 and saved value of b1.
```

如果你想要给graph增加更多的操作（operations）然后训练它，可以像如下那么做：

```python
import tensorflow as tf

sess=tf.Session()    
#First let's load meta graph and restore weights
saver = tf.train.import_meta_graph('my_test_model-1000.meta')
saver.restore(sess,tf.train.latest_checkpoint('./'))


# Now, let's access and create placeholders variables and
# create feed-dict to feed new data

graph = tf.get_default_graph()
w1 = graph.get_tensor_by_name("w1:0")
w2 = graph.get_tensor_by_name("w2:0")
feed_dict ={w1:13.0,w2:17.0}

#Now, access the op that you want to run. 
op_to_restore = graph.get_tensor_by_name("op_to_restore:0")

#Add more to the current graph
add_on_op = tf.multiply(op_to_restore,2)

print sess.run(add_on_op,feed_dict)
#This will print 120.	
```

但是，你可以只恢复旧的graph的一部分，然后插入一些操作用于fine-tuning？当然可以。仅仅需要通过 by graph.get_tensor_by_name() 方法来获取合适的operation，然后在这上面建立graph。下面是一个实际的例子，我们使用meta graph 加载了一个预训练好的vgg模型，并且在最后一层将输出个数改成2，然后用新的数据fine-tuning。

```python
......
......
saver = tf.train.import_meta_graph('vgg.meta')
# Access the graph
graph = tf.get_default_graph()
## Prepare the feed_dict for feeding data for fine-tuning 

#Access the appropriate output for fine-tuning
fc7= graph.get_tensor_by_name('fc7:0')

#use this if you only want to change gradients of the last layer
fc7 = tf.stop_gradient(fc7) # It's an identity function
fc7_shape= fc7.get_shape().as_list()

new_outputs=2
weights = tf.Variable(tf.truncated_normal([fc7_shape[3], num_outputs], stddev=0.05))
biases = tf.Variable(tf.constant(0.05, shape=[num_outputs]))
output = tf.matmul(fc7, weights) + biases
pred = tf.nn.softmax(output)

# Now, you run this with fine-tuning data in sess.run()	
```















