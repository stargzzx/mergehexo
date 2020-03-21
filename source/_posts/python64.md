---
title: python 安装 pytorch
date: 2019-11-10 09:23:01
categories:
- python
- 模块
- pytorch
tags:
- python
- torch
- pytorch
---
这里将讲述关于pytorch的事。

<!-- more -->

我们如果直接下载 pytorch 会非常慢，因为原因你懂的，所以，我们要添加专属于 pytorch 的镜像。

	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
	conda config --set show_channel_urls yes
	
然后我们需要检查自己的 cuda 是什么版本。

win的查找方式自己找，现在我们只说一下 linux 的版本。

这里提供两个办法。

第一个是

	cat /usr/local/cuda/version.txt
	
第二个是

	nvcc -V
	
这里有一个有意思的地方就是，两个方法查找的版本不一致，如果安装错了版本，导致 pytorch 和 cuda 的版本不一样，就会触发 assert 错误。

在这个我建议以

	ncvv -V
	
为准，另外，有的系统可能没有其中一个方法，这属于正常现像。

得到 cuda 的版本后，我们就要得到相应版本的 pytorch。

访问官网。

[pytorch](https://pytorch.org/)

![](/images/python/64_0.png)

我们将会选择合适的系统和相应版本的命令，但是，如果没有找到对应版本，千万别胡乱安装，我们可以点击图中红框部分，查找之前的 pytroch 版本。

要知道 9.0.1 和9.0.2 也是完全不同的版本。

另外，我们可以看到安装命令是

	conda install pytorch torchvision cudatoolkit=10.1 -c pytorch
	
我们要把 -c 给去掉，变成下面的那样

	conda install pytorch torchvision cudatoolkit=10.1
	
加上 -c 是指从 pytorch 原官网下载，而不是利用镜像。

