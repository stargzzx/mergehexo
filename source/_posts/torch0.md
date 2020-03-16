---
title: pytorch | 入门
date: 2020-01-22 18:10:58
categories:
- pytorch
tags:
- pytorch
---
正式开始学习 torch。

因为听说 tensorflow 对 debug 支持的不够好，另外，2.0 的 tensorflow 貌似更差了，所以，转向了 pytorch。

在这里记录一下学习历程。另外，本次学习的主线是根据 莫烦 python 来的。

<!-- more -->

<br/>

# 学习资料

<br/>

[pytorch-tutorial](https://github.com/yunjey/pytorch-tutorial)
[DEEP LEARNING WITH PYTORCH: A 60 MINUTE BLITZ](https://pytorch.org/tutorials/beginner/deep_learning_60min_blitz.html)

<br/>

# 安装 torch

<br/>

你可以访问我下面的 blog ，安装它还是需要一点技巧的。

[安装 torch](https://benpaodewoniu.github.io/2019/11/10/python64/)

<br/>

# 对于 macos

<br/>

官网上说

>MacOS Binaries dont support CUDA, install from source if CUDA is needed

上面话的意思是，macos 不支持 cuda ，所以，其没有 gpu 加速。无论你选择哪个版本，最终安装的都是一个版本， cpu 版本。

安装命令如下：

	pip3 install torch torchvision

我们发现其安装了 torch 和 torchvision。

其中，我们先讲一下 torchvision。

<br/>

# torchvision

<br/>

这是一个独立与 torch 的库，里面包含了很多经典的网络机构和数据集。如，已经训练好的 vgg 结构，常用的数据集等等。

	torchvision.datasets
		几个常用视觉数据集，可以下载和加载，这里主要的高级用法就是可以看源码如何自己写自己的Dataset的子类
	torchvision.io
	torchvision.models
		流行的模型，例如 AlexNet, VGG, ResNet 和 Densenet 以及 与训练好的参数。
	torchvision.ops
	torchvision.transforms
		常用的图像操作，例如：随机切割，旋转，数据类型转换，图像到tensor ,numpy 数组到tensor , tensor 到 图像等。
	torchvision.utils
		用于把形似 (3 x H x W) 的张量保存到硬盘中，给一个mini-batch的图像可以产生一个图像格网。

由于此包是配合pytorch的对于图像处理来说必不可少的，

对于以后要用的torch来说一站式的anaconda是首选，毕竟人生苦短。

<br/>

# numpy 和 torch 的区别

<br/>

```python
import torch
import numpy as np

np_data = np.arange(6).reshape((2, 3))
torch_data = torch.from_numpy(np_data)
tensor2array = torch_data.numpy()
print(np_data, '\n', torch_data, '\n', tensor2array)

	[[0 1 2]
 	[3 4 5]] 
 	tensor([[0, 1, 2],
        	[3, 4, 5]]) 
	 [[0 1 2]
 	[3 4 5]]
```



