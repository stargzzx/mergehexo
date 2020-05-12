---
title: gpu 的驱动，cuda，cudnn的关系
date: 2019-12-07 21:50:19
categories:
- 人工智能
- 基本知识
tags:
- nvidia
- gpu
- cuda
- cudnn
---
这个讲述了 nvidia 和 驱动，gpu 之间的关系。

<!-- more -->

如果你对 cpu 和 gpu 之间的关系感兴趣，请阅读：

[CPU 和 GPU 的区别](https://benpaodewoniu.github.io/2018/08/26/basis6/)

<br/>

# 什么是显卡？

<br/>

显卡（Video card，Graphics card）全称显示接口卡，又称显示适配器，是计算机最基本配置、最重要的配件之一。就像电脑联网需要网卡，主机里的数据要显示在屏幕上就需要显卡。因此，显卡是电脑进行数模信号转换的设备，承担输出显示图形的任务。具体来说，显卡接在电脑主板上，它将电脑的数字信号转换成模拟信号让显示器显示出来。

原始的显卡一般都是集成在主板上，只完成最基本的信号输出工作，并不用来处理数据。随着显卡的迅速发展，就出现了GPU的概念，显卡也分为独立显卡和集成显卡。

<br/>

# 什么是GPU？

<br/>

GPU这个概念是由Nvidia公司于1999年提出的。GPU是显卡上的一块芯片，就像CPU是主板上的一块芯片。那么1999年之前显卡上就没有GPU吗？当然有，只不过那时候没有人给它命名，也没有引起人们足够的重视，发展比较慢。

自Nvidia提出GPU这个概念后，GPU就进入了快速发展时期。简单来说，其经过了以下几个阶段的发展：

1.仅用于图形渲染，此功能是GPU的初衷，这一点从它的名字就可以看出：Graphic Processing Unit，图形处理单元；

2.后来人们发现，GPU这么一个强大的器件只用于图形处理太浪费了，它应该用来做更多的工作，例如浮点运算。怎么做呢？直接把浮点运算交给GPU是做不到的，因为它只能用于图形处理（那个时候）。最容易想到的，是把浮点运算做一些处理，包装成图形渲染任务，然后交给GPU来做。这就是GPGPU（General Purpose GPU）的概念。不过这样做有一个缺点，就是你必须有一定的图形学知识，否则你不知道如何包装。

3.于是，为了让不懂图形学知识的人也能体验到GPU运算的强大，Nvidia公司又提出了CUDA的概念。

<br/>

# 什么是CUDA？

<br/>

CUDA(ComputeUnified Device Architecture)，是显卡厂商NVIDIA推出的运算平台。 CUDA是一种由NVIDIA推出的通用并行计算架构。它包含CUDA指令集架构以及GPU内部的并行计算引擎。你只要使用一种类似于C语言的CUDA C语言，就可以开发CUDA程序，从而可以更加方便的利用GPU强大的计算能力，而不是像以前那样先将计算任务包装成图形渲染任务，再交由GPU处理。

换句话说CUDA是NVIDIA推出的用于自家GPU的并行计算框架，也就是说CUDA只能在NVIDIA的GPU上运行，而且只有当要解决的计算问题是可以大量并行计算的时候才能发挥CUDA的作用。

注意，并不是所有GPU都支持CUDA。

<br/>

# 什么是CUDNN？

<br/>

NVIDIA cuDNN是用于深度神经网络的GPU加速库。它强调性能、易用性和低内存开销。NVIDIA cuDNN可以集成到更高级别的机器学习框架中，如谷歌的Tensorflow、加州大学伯克利分校的流行caffe软件。简单的插入式设计可以让开发人员专注于设计和实现神经网络模型，而不是简单调整性能，同时还可以在GPU上实现高性能现代并行计算。

如果你要用GPU训练模型，cuDNN不是必须的，但是一般会采用这个加速库。

<br/>

# CUDA与CUDNN的关系？

<br/>

CUDA看作是一个工作台，上面配有很多工具，如锤子、螺丝刀等。cuDNN是基于CUDA的深度学习GPU加速库，有了它才能在GPU上完成深度学习的计算。它就相当于工作的工具，比如它就是个扳手。但是CUDA这个工作台买来的时候，并没有送扳手。想要在CUDA上运行深度神经网络，就要安装cuDNN，就像你想要拧个螺帽就要把扳手买回来。这样才能使GPU进行深度神经网络的工作，工作速度相较CPU快很多。

## CUDNN不会对CUDA造成影响

官方Linux安装指南表述：

![](/images/nvidia/0_0.png)

从官方安装指南可以看出，只要把cuDNN文件复制到CUDA的对应文件夹里就可以，即是所谓插入式设计，把cuDNN数据库添加CUDA里，cuDNN是CUDA的扩展计算库，不会对CUDA造成其他影响。

cuDNN的安装文件有两个文件夹，共五个文件，如下

![](/images/nvidia/0_1.png)

>cudnn.h是调用加速库的文件，*.os是

CUDA平台里对应文件夹的文件，如下

![](/images/nvidia/0_2.png)

可以看到，CUDA已有的文件与cuDNN没有相同的文件，复制CUDNN的文件后，CUDA里的文件并不会被覆盖，CUDA其他文件并不会受影响。

<br/>

# 总结

<br/>

简单来说，CPU适合串行计算，擅长逻辑控制。GPU擅长并行高强度并行计算，适用于AI算法的训练学习

CUDA 是NVIDIA专门负责管理分配运算单元的框架

cuDNN是用于深层神经网络的gpu加速库

<br/>

# 查询版本命令：

<br/>

## ubuntu:

cuda 版本

	nvcc -V
	cat /usr/local/cuda/version.txt

cudnn 版本 

	cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2

查看Nvidia GPU版本

	nvidia-smi
	lspci | grep -i nvidia

查看NVIDIA驱动版本

	sudo dpkg --list | grep nvidia-*
