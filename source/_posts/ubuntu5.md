---
title: ubuntu | 安装显卡驱动和CUDA
date: 2020-09-01 12:45:56
categories:
- [linux,ubuntu]
tags:
- ubuntu
---
网上的教程玲琅满目，但是，大都是从同一篇教程中 copy 的，不可信。

以下，完全是我成功之后的方案。

<!-- more -->

<br/>

# 环境介绍

<br/>

- amax 小型服务器
- 双系统
	- win 10
	- ubuntu 18.04
- GTX 1080ti


<br/>

# 流程

<br/>

记得联网。

整个过程我重装了 3 次系统。关于如何重装系统，请参考

- [ubuntu 的安装](https://benpaodewoniu.github.io/2019/01/19/ubuntu0/)

## 检查是否有 GTX 显卡

	lspci | grep -i nvidia

输出

	65:00.0 VGA compatible controller: NVIDIA Corporation GP102 [GeForce GTX 1080 Ti] (rev a1)
	65:00.1 Audio device: NVIDIA Corporation GP102 HDMI Audio Controller (rev a1)

可以看我，我的显卡是 `GTX 1080 Ti` 。符合， `cuda` 加速显卡。

这个时候我们查看一下驱动。「驱动是让你的系统可以识别显卡，不然只能使用集显」

输入

	nvidia-smi

如果有驱动的话，就会出现类似于下面这种

	Tue Sep  1 14:23:16 2020       
	+-----------------------------------------------------------------------------+
	| NVIDIA-SMI 440.100      Driver Version: 440.100      CUDA Version: 10.2     |
	|-------------------------------+----------------------+----------------------+
	| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
	| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
	|===============================+======================+======================|
	|   0  GeForce GTX 108...  Off  | 00000000:65:00.0  On |                  N/A |
	| 27%   36C    P8    18W / 250W |    184MiB / 11175MiB |     11%      Default |
	+-------------------------------+----------------------+----------------------+
	                                                                               
	+-----------------------------------------------------------------------------+
	| Processes:                                                       GPU Memory |
	|  GPU       PID   Type   Process name                             Usage      |
	|=============================================================================|
	|    0      1100      G   /usr/lib/xorg/Xorg                           100MiB |
	|    0      1336      G   /usr/bin/gnome-shell                          77MiB |
	|    0      1834      G   gnome-control-center                           2MiB |
	+-----------------------------------------------------------------------------+

但是，如果没有安装的话，可能啥也没有，也可能会出现一个提示，让你安装啥，这时候千万别安装。

因为，使用 `apt-get` 安装某一个驱动，会让后续工作变得非常麻烦，而且，可能版本老旧，让你运行不了「我因为使用提示安装，重装了系统」。

有的，教程，这个时候让你使用 `nvcc -V` 来查看是不是有显卡驱动和 `CUDA` 的。

如果，你没有，此时也可能提供一个提示命令，比如，我的是

	sudo apt-get install nvidia-cuda-toolkit

千万别弄，弄了会崩。

此时此刻，我们应该静下心来。先搞懂，驱动我们应该安装啥版本。

使用命令

	ubuntu-drivers devices

出现

	== /sys/devices/pci0000:64/0000:64:00.0/0000:65:00.0 ==
	modalias : pci:v000010DEd00001B06sv00001043sd000085E5bc03sc00i00
	vendor   : NVIDIA Corporation
	model    : GP102 [GeForce GTX 1080 Ti]
	driver   : nvidia-driver-435 - distro non-free
	driver   : nvidia-driver-390 - distro non-free
	driver   : nvidia-driver-440 - distro non-free
	driver   : nvidia-driver-440-server - distro non-free recommended
	driver   : nvidia-driver-418-server - distro non-free
	driver   : xserver-xorg-video-nouveau - distro free builtin

可以看到，在 `1080ti` 下 440 版本是优先推荐的。

如果，之前你根据提示来安装的话，可能会安装 390 版本的。「亲测，重装了系统。」

## 命令行安装「不推荐」

这个时候，有的教程会推荐使用

	sudo ubuntu-drivers autoinstall 

这个时候，我预测会安装好 440 的驱动，我没试过，我也不建议你尝试，因为，我有更好的方法。

此时此刻，我们知道了最好的驱动是 440 。假设，你使用 `sudo ubuntu-drivers autoinstall` 来安装驱动，那么，最后大概率只有驱动能安装上，而 `CUDA` 是安装不上的。

也就是，你使用

	nvidia-smi

只能看到 `driver` 。所以，你后面还需要装 `CUDA`。

既然，搞到了这里，我们就继续看一下， CUDA 和 驱动的关系。

当你，安装完驱动后，一定要找相应系统相应版本的 `CUDA`。

你可以查看我下面的博客。

- [cuda、cudnn版本的关系](https://benpaodewoniu.github.io/2019/12/07/nvidia1/)

但是，我更建议你直接看官方的「链接在上面的博文中」。

|CUDA Toolkit|	Linux x86_64 Driver Version|	Windows x86_64 Driver Version|
|---|---|---|
|CUDA 11.0.3 Update 1	|>= 450.51.06	|>= 451.82|
|CUDA 11.0.2 GA	|>= 450.51.05	|>= 451.48|
|CUDA 11.0.1 RC	|>= 450.36.06	|>= 451.22|
|CUDA 10.2.89	|>= 440.33	|>= 441.22|

可以看到，驱动 440 的时候， `CUDA` 的版本是 `10.2`。

所以，你安装完驱动还需要去 nvidia 官网,安装 `CUDA`。

- [CUDA工具包档案](https://developer.nvidia.com/cuda-toolkit-archive)

![](/images/ubuntu/5_1.png)

根据版本来安装完 CUDA。 此时此刻，我预测应该可以了，但是，这样做，总会出意外。所以，我不建议你这么做。

另外需要注意的是，如果按照这个方法安装 CUDA 下载 的 nvida cuda 安装包，可能自带驱动，所以，安装的过程要把驱动取消，否则会出问题。

后面还要配置环境变量之类的，如果你打算走这条路，建议你好好看一下其他的博客指导。

- [Ubuntu18.04下安装CUDA和cudnn](https://www.jianshu.com/p/00c37b09f0f3)

我更推荐你使用图形化界面的做法。

## 图形化安装「推荐」

图形化安装非常简单。

找到

- 软件和更新「或许叫其他名字，类似的软件点开看一看」

出现下面。

<div style="width: 50%;padding-left: 25%">

![](/images/ubuntu/5_0.png)

</div>

附加驱动，然后选择相应的驱动版本。点击应用更改，就会自己下载，时间挺长的，下载完，重启就好了。

这个时候，你再 `nvidia-smi`

就会发现

- 驱动
- cuda

都装好了。非常舒服。

ps: 里面某些具体细节，我都忘了，我记得当时，我只是直接选了上面的 GUI 操作，然后就成功了。

但是，不排除我遗忘了一些细节。

我看其他博客说，如果要这么安装还要添加一些存储库，我记忆中是不需要的，但是，保不齐真的需要，所以，贴一下相关的博文。

- [ubuntu18.04安装与更新NVIDIA驱动](https://www.cnblogs.com/answerThe/p/12252464.html)

## nvidia官网下载全集「不推荐」

这个不推荐，也没必要，所以，不再写。

安装完之后，执行

	nvidia-smi

应该可以看到驱动和 CUDA 版本。