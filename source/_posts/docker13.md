---
title: nvidia-docker | 简介和扫盲
date: 2020-04-24 21:55:24
categories:
- [docker,进阶,nvidia-docker]
tags:
- docker
- nvidia-docker
---
这个系列我一直想写，但是，没敢动笔，一个是因为我现在并不想做深度学习，另外一个是当初学了一会，感觉 `nvidia-docker` 比 `docker` 难很多，于是就放弃了。

但是，真男人敢于面对自己的短处，并克服它，所以，在这里，我将走出舒适区。

<!-- more -->

<br/>

# 介绍

<br/>

[nvidia-docker简介](https://nanxiao.me/docker-note-13-nvidia-docker-intro/)

因为`GPU`属于特定的厂商产品，需要特定的`driver`，`Docker`本身并不支持`GPU`。以前如果要在`Docker`中使用`GPU`，就需要在`container`中安装主机上使用GPU的`driver`，然后把主机上的`GPU`设备（例如：`/dev/nvidia0`）映射到`container`中。所以这样的`Docker image`并不具备可移植性。

`Nvidia-docker`项目就是为了解决这个问题，它让`Docker image`不需要知道底层`GPU`的相关信息，而是通过启动`container`时`mount`设备和驱动文件来实现的。

参考官方给的资料

[nvidia-docker](https://github.com/NVIDIA/nvidia-docker)

>Make sure you have installed the NVIDIA driver and Docker 19.03 for your Linux distribution Note that you do not need to install the CUDA toolkit on the host, but the driver needs to be installed

在你使用 `ndocker` 的时候，你需要做的有两件事

- 安装你电脑中 `GPU` 的 `driver`
- 安装 `docker 19.03`
- 你不需要安装 `CUDA`
	- 集成到 `nvidia-docker` 了

## nvidia-docker 的发展历程

初学者第一次看 `ndocker` 的时候，会有一个疑问，那就是 `nvidia-docker` 和 `nvidia-docker2` 的区别是什么？

这其实是一个坑。

最开始，`nvidia-docker` 需要运行一个独立的 `daemon`，与 `Docker` 的生态不能很好地兼容。 比如，`docker-compose`、`docker swarm` 与 `Kubernetes`，都不能很好的和 `nvidia-docker` 一起工作。

所以，第一代的 `nvidia-docker` 被弃用。

官方，研发出 `nvidia-docker2`.

`nvidia-docker` 一代是一个 `Volume Plugin`，而 `nvidia-docker2` 则是一个 `Docker Runtime`，机制的差异，带来了巨大的改进。

后来，`docker 19.03` 发布后，`NVIDIA GPU` 本身作为 `Docker` 运行时中的设备受支持，所以 `nvidia-docker2` 又被弃用。

现在的 `nvidia-docker` 是建立在 `docker 19.03` 上的。

官方提供了，nvidia-docker2 升级步骤

[Upgrading with nvidia-docker2](https://github.com/NVIDIA/nvidia-docker/tree/master#upgrading-with-nvidia-docker2-deprecated)

不建议深度学习部署在 windows 上，这样会引发一些不可调控的问题。

## 安装

查看该机器是否已经成功安装了 `nvidia-docker`

可以使用

    nvidia-docker run -–rm nvidia/cuda nvidia-smi

如果没有 nvidia/cuda docker 会自己下载的，最后出现。

![](/images/docker/13_0.png)

ps: 2020-09-03 上面的命令并不能下载，所以，我有查阅了官网的安装资料。

- [nvidia-docker 安装流程](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker)

官网明确说过，如果你有 `nvidia-docker1.0` 要么升级，要么卸载后安装 `nvidia-docker2`。

我们是通过 `apt-get` 安装。但是，`apt-get` 的下载通道中没有这个下载通道，所以，我们要添加一下。

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```

这个时候已经添加了。

然后安装 `nvidia-docker2`。

```
sudo apt-get update
sudo apt-get install -y nvidia-docker2
```

重新打开 

```
sudo systemctl restart docker
```

然后官网会给你一个命令检测你安装成功了吗。

	sudo docker run --rm --gpus all nvidia/cuda:11.0-base nvidia-smi

这个命令不一定可以，因为，你的驱动可能太低了，不能运行 `cuda11.0`。所以，你可以自己尽心降版本运行。

	sudo docker run --rm --gpus all nvidia/cuda:10.0-base nvidia-smi
	sudo docker run --rm --gpus all nvidia/cuda:10.2-base nvidia-smi

祝你好运。