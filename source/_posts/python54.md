---
title: python 的 cPickle pickle
date: 2019-02-23 10:49:58
categories:
- python
tags:
- python
- python 模块
- python模块
- cPickle
- pickle
---
这是 python 自带模块， pickle 的教程。

<!-- more -->

<br/>

# 参考资料

<br/>

[python import cPickle 时提示没有这个模块，请问怎么解决](https://zhidao.baidu.com/question/811568894199979732.html)
[python 3.X 没有cPickle](https://blog.csdn.net/menuconfig/article/details/8672367)
[关于python3中该如何安装cPickle](https://blog.csdn.net/CaoMei_HuaCha/article/details/82899662)
[python数据持久存储：pickle模块的基本使用](https://www.cnblogs.com/pannyvan/p/4439308.html)

<br/>

# 小知识

<br/>

cPickle在python3中更名为pickle了

# Pickle与CPickle对比

前者是完全用Python来实现的模块，这个CPickle是用C来实现的，它的速度要比pickle快好多倍，一般建议如果电脑中只要有CPickle的话都应该使用它。

<br/>

# 作用

<br/>

经常遇到在Python程序运行中得到了一些字符串、列表、字典等数据，想要长久的保存下来，方便以后使用，而不是简单的放入内存中关机断电就丢失数据。

这个时候Pickle模块就派上用场了，它可以将对象转换为一种可以传输或存储的格式。

python的pickle模块实现了基本的数据序列和反序列化。通过pickle模块的序列化操作我们能够将程序中运行的对象信息保存到文件中去，永久存储；通过pickle模块的反序列化操作，我们能够从文件中创建上一次程序保存的对象。

<br/>

# 函数

<br/>

## D

### dump

pickle.dump(对象, 文件，[使用协议])

将要持久化的数据“对象”，保存到“文件”中，使用有3种，索引0为ASCII，1是旧式2进制，2是新式2进制协议，不同之处在于后者更高效一些。

默认的话dump方法使用0做协议。


## L

### load

load()方法的作用正好与上面的dump()方法相反，上面是序列化数据，这个方法作用是反序列化。

pickle.load(文件)

提示：从“文件”中，读取字符串，将它们反序列化转换为Python的数据对象，可以正常像操作数据类型的这些方法来操作它们。

但是在使用的时候可能会遇到编码错误，请参考我下面的博文。

[python 编码异常](https://benpaodewoniu.github.io/2019/02/23/python53/)













