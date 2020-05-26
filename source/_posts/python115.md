---
title: python | pypy 必须知道的问题
date: 2020-05-26 17:26:29
categories:
- python
- 进阶
- pypy
tags:
- pypy
---
这里列了一下 pypy 新手可能会有疑问的问题。

<!-- more -->

## 官网

- [pypy](https://www.pypy.org/)

## 安装 pypy

如果你去官网的下载地址

- [Download and Install](https://www.pypy.org/download.html)

可以找到相应的下载版本，截止到我写这篇文章的时候，pypy 提供了两个版本

- 2.7
- 3.6

因为使用的是 ubuntu ，python3 所以，找到相应的下载链接，需要注意的是，不同的 linux 版本需要下载不同的资源，在官网上有一句话是这样说的

>Some Linux distributions provide a pypy package. Note that in order to install additional modules that require compilation, you may need to install additional packages such as pypy-dev. This will manifest as an error about “missing Python.h”. Distributions do not as of yet supply many pypy-ready packages, if you require additional modules we recommend creating a virtualenv and using pip.

>stating it again: the Linux binaries are provided for the distributions listed here. If your distribution is not exactly this one, it won't work, you will probably see: pypy: error while loading shared
libraries: ....

所以，要注意 centos，ubuntu 需要不同的下载二进制资源。从官网上看目前，只提供了 ubuntu18.04 所以，不清楚这个是否能够支持 16.04 ，于是，考虑走 `apt-get` 路线。

## ubuntu16.04 安装

登陆 ubuntu 官网。

- [ubuntu](https://ubuntu.com/)

搜索 `pypy3`

可以得到下面的链接

- [How to install pypy3 on Ubuntu](https://snapcraft.io/install/pypy3/ubuntu)

里面有这样的话

If you’re running Ubuntu 16.04 LTS (Xenial Xerus) or later, including Ubuntu 18.04 LTS (Bionic Beaver), Ubuntu 18.10 (Cosmic Cuttlefish) and Ubuntu 19.04 (Disco Dingo), you don’t need to do anything. Snap is already installed and ready to go.

For versions of Ubuntu between 14.04 LTS (Trusty Tahr) and 15.10 (Wily Werewolf), as well as Ubuntu flavours that don’t include snap by default, snap can be installed from the Ubuntu Software Centre by searching for snapd.

Alternatively, snapd can be installed from the command line:

    sudo apt update
    sudo apt install snapd

Either log out and back in again, or restart your system, to ensure snap’s paths are updated correctly.

Install pypy3

To install pypy3, simply use the following command:

    sudo snap install pypy3 --classic

## pypy 的包的兼容性

CPython 之所以成为现在最流行的语言，就是其有大量的第三方扩展包，那么，如何知道 CPython 的扩展包是否可用呢？

其官方提供了一份名单

- [packages](http://packages.pypy.org/)

但是，目前这个列表并没有明确说明哪些包可以用哪些不可以用，现阶段，我给的建议就是试错。

## 如何 install 包

我们应该先为 pypy 提供 pip。

- [PIP安装教程](https://pip.pypa.io/en/latest/installing/)

<br/>

    wget https://bootstrap.pypa.io/get-pip.py
    pypy get-pip.py

至此，pip就安装完成了！

为了防止pypy pip 与 与python的 pip 冲突。 

PyPy的pip使用方法如下：

    pypy -m pip install package 