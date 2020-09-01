---
title: 开源项目 | avatarify
date: 2020-09-01 15:19:14
categories:
- [开源项目,深度学习,avatarify]
tags:
- avatarify
---
这个是 `first-order-model` 的升级版本。

你可以看我下面的博文，来了解这个项目。

- [first_order_model](https://benpaodewoniu.github.io/2020/05/28/first-order-model0/)

这个项目做到了可以实时进行表情迁移。

<!-- more -->

首先你要确保，你的电脑有 GTX 显卡，还有 CUDA 。

我的电脑是

- GTX 1080 Ti
- drivers 440
- cuda 10.2

你可以查看我下面的博文，来安装相应的驱动和 `CUDA`。

- [ubuntu | 安装显卡驱动和CUDA](https://benpaodewoniu.github.io/2020/09/01/ubuntu5/)

OK，让我们正式进入安装 `avatarify` 的流程。

首先介绍一下，我的环境

- ubuntu 18.04

<br/>

# 安装

<br/>

- [avatarify 项目地址](https://github.com/alievk/avatarify)

另，我看这个好像有 docker 版本，但是，我没弄，我是直接安装。

## 安装 Miniconda

如果你是 `anaconda` ，这一步可以跳过。

但是，Miniconda 轻量级，快速部署就得了，应付公事。

- [Linux installers](https://docs.conda.io/en/latest/miniconda.html#linux-installers)

下载最新版本。

然后安装

	bash Miniconda3-latest-Linux-x86_64.sh

安装完之后，更换一下源。

- [Anaconda 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

但是，我使用这个的时候，出现了点错误， `torch` 找不到，所以，我给修改了一下，详情参考，我下面的博文。

- [python 安装 pytorch](http://localhost:4000/2019/11/10/python64/)

## clone 项目

	git clone https://github.com/alievk/avatarify.git
	cd avatarify

在执行，安装命令前，我们要对 `scripts/install.sh` 进行修改。

把

	conda install -y pytorch==1.0.0 torchvision==0.2.1 cuda100 -c pytorch
	conda install -y python-blosc==1.7.0 -c conda-forge

改成

	conda install -y pytorch==1.0.0 torchvision==0.2.1 cuda100
	conda install -y python-blosc==1.7.0

如果是原来的，那么会从 `torch` 官网进行下载，不会从源下载。

然后执行

	bash scripts/install.sh「执行这个步骤，要注意执行的地方，就在 avatarify 文件夹中」

这一步会安装大量的东西，没得办法，只能根据这个安装。

如果，多次安装不成功「网速不行」，可以看看是哪里不成功，然后，单独安装那些东西。

其中有一个项目是 `fomm` 这个就是 `first-order-model` ，如果多次下载不成功，可以单独下载，然后改名后放在 `avatarify` 文件夹中。「我没这样试过，但是，我猜测可以。。。」

另外，我们要把 `first order model` 的那个 `vox-adv-cpk.pth.tar` 放在 `avatarify` 项目「项目根目录」中。

## 检查摄像头

我们连接摄像头，看一下，你的设备是否可以，连接完之后，使用命令

	cheese

如果没有，ubuntu 会提示你安装的，然后，如果摄像头可以查看，就说明没问题。

## 运行

进入虚拟环境

	conda activate avatarify

然后执行

	bash run.sh

在第一次启动的时候，还会下载东西，这个东西是某一个第三方库的东西。

- CNN
- FAN

因为墙和网速的原因不一定能一口气下载下来，没办法。。。只能多次尝试，一共大概 「140MB」大小。

我这个还算顺利，我按照流程直接就能成功。「第一次开启，按照提示的要求来弄就好了」

在，项目的 `README.md` 中有这样一段描述。

	The run script will create virtual camera /dev/video9. You can change these settings in scripts/settings.sh.
	You can use command v4l2-ctl --list-devices to list all devices in your system.

如果遇到问题，我建议你看一下项目描述。

另外，这个体验最好的就是 1080ti ，如果没有视频卡加速，那么，会 2 秒才能处理一个画面，不能用。

<br/>

# 技巧

<br/>

## 图片放置

把你的图片放在项目的

- avatars 

文件夹中，英文命名，系统会自动检测图片名字。

|按键|作用|
|---|---|
|数字键|切换到第几个图片|
|w|镜头拉近|
|s|镜头拉远|
|a|上一张图片|
|d|下一张图片|
|x|重新人脸对焦|
|I|显示数值，装逼用|
|esc|退出|