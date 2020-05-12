---
title: MacBook | 安装 kafka
date: 2020-02-10 00:35:34
categories:
- 中间件
- kafka
tags:
- kafka
- MacBook
---
这个博文讲述了如何在 macbook 上安装kafka。

<!-- more -->

整个安装流程非常简单，但是，我的电脑想让我先安装 jdk8，即运行下面的命令：

	brew cask install homebrew/cask-versions/adoptopenjdk8

我在这一步上遇到了很大的困难，进过我的 brew 的源已经替换了，但是我不清楚通过 cask 是否也能走源。

所以，这一步下载速度非常慢，在这里我附上我的解决方案。

[MacBook | brew 本地安装](https://benpaodewoniu.github.io//2020/02/10/macbook8/)

安装好 jdk8 之后，后面就变得异常简单，直接运行命令

	brew install kafka

其就能把所有的依赖帮你装好了。


