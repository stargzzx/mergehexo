---
title: tensorflow中的坑 无法在 python2 中安装
date: 2019-01-25 14:27:49
categories:
- tensorflow
tags:
- machine learning
- tensorflow
---
这个问题，搞的我都快疯了，最终在官网文档中找到了解决方案。

<!-- more -->

## 参考资料

[Install TensorFlow with pip](https://www.tensorflow.org/install/pip)

## 问题描述

### 在 anaconda3 中安装 tensorflow

我创建了一个 python2.7 版本的环境，但是在安装 tensorflow 中，总会自动升级 python 版本到 3.5.6

![](/images/tensorflow/14_0.png)

于是，我卸载了 3 的版本，安装了 anaconda2 的版本，以为是环境问题，但是很不幸。

我连配置虚拟环境都出错了，我是感到很崩溃。。。

### 用 virtualenv 下载 tensorflow

原来我以为是 anaconda 太过于智能，自动升级，所以，我用 virtualenv 配置了一个 2.7 环境。

但是在 pip 的时候还是失败了。。。

## 问题所在

在查了官方文档，就是上面的链接。上面有这么一句话
>Windows 7 or later (64-bit) (Python 3 only)

OK,就这样。

## 在 windows 下安装

[Build from source on Windows](https://www.tensorflow.org/install/source_windows)

>Install a Python 3.5.x or Python 3.6.x 64-bit release for Windows. Select pip as an optional feature and add it to your %PATH% environmental variable.














