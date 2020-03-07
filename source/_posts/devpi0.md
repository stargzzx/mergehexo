---
title: devpi | 本地安装
date: 2020-02-10 00:53:48
categories:
- devpi
tags:
- devpi
- python
---
这个是本地安装并且成功使用，并且这篇教程还包含如何使用 devpi 以及一些技巧和我自己的总结。

先说一下背景，我的本地电脑是 MacBook。安装的 devpi 版本是 5.1.1。

<!-- more -->

<br/>

# 安装

<br/>

安装没有任何难度，直接就是官方的教程，这里特别提醒一下，现在网上很多 devpi 教程都已经过时了，所以，要想安装，首先看看官方的最新版是否一样。

[Quickstart: uploading, testing, pushing releases](https://devpi.net/docs/devpi/devpi/5.2/+d/quickstart-releaseprocess.html#installing-devpi-client-and-server)

安装命令

	pip install -U devpi-web devpi-client

因为我是 anaconda 的虚拟环境，所以，我先进的虚拟环境，然后安装的，无压力，就是安装的依赖太多了，挺耗时间的。

在这里建议跟着官方教程一起走，因为官方教程会出现每一步执行命令后的提示，方便查看还有没有执行错误

<br/>

# 初始化

<br/>

	devpi-init

	devpi-gen-config

	devpi use http://localhost:3141

	(创建用户)

	devpi user -c ant password=123

	devpi login ant --password=123

	devpi index -c dev bases=root/pypi

	devpi use testuser/dev 【上面这些命令在官方教程中有详细的解释，在这里不再陈述】

至此 devpi 就已经启动完毕。

我们使用浏览器访问，便会看到：

{% img /images/devpi/0_0.png %}

这里特别提醒一点是，上面的用户排序是根据 26 个字母顺序来排的。

<br/>

# 上传自己的包

<br/>

因为，我们搭建私服，本身就是为了打包自己的项目。经过，公司的大牛编写的脚本，我在本地生成了这个包。

{% img /images/devpi/0_1.png %}

查阅相关资料

进入你其中一个项目的 setup.py 文件的目录（我们假设名字为 example） 来构建和上传你的包到 ant/dev 索引。

可以看到，那个项目中包含 setup.py ，所以进入这个目录后，我们直接执行

	devpi upload

可以看到顺利的上传了自己的包。

而，看浏览器的 devpi ant 用户的目录，可以发现

{% img /images/devpi/0_2.png %}

<br/>

# 安装自己的包

<br/>

进入虚拟环境之后，我们可以使用下面两个方式安装

## 方式一

	devpi install ant

## 方式二

	pip install -i http://localhost:3141/ant/dev ant

这两个版本都可以。

<br/>

# 技巧

<br/>

有的时候，我们想要找 devpi 很多命令，但是，网上的资料是有限的，比如，如何删除 devpi 用户，我找了好久还没找到，迫不得已，我使用了自带的 help ，但是，用过之后才发现，是那么的好用。

执行

	devpi --help

{% img /images/devpi/0_3.png %}

我们可以看到很多命令，我们可以发现 user 这个命令段，为了知道这个命令怎么用，我们再执行

	devpi user --help

{% img /images/devpi/0_4.png %}

就可以很轻松的知道，如何删除 user 了

	devpi user ant --delete
		（在删除之前需要登录）

而且，如果你选择看官方文档的话，更会节省很多力气，所以，不要怕英文，试着走出自己的舒适区。

加油！！！共同进步！！！

[官方文档](https://devpi.net/docs/devpi/devpi/5.2/+d/index.html)

