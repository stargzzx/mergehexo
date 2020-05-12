---
title: anaconda | 配置文件 .condarc
date: 2020-01-09 16:16:15
categories:
- [python,中间件,anaconda]
tags:
- anaconda
- condarc
---
这个是你了解 anaconda 运行必须要知道的文件。

<!-- more -->

我们经常会配置一些参数，比如配置清华源，不让进入环境就自动激活环境，那么这一切又该如何配置呢？

所有的一切都离不开一个文件 .condarc

这个文件的位置

<br/>

# macbook、linux

<br/>

在 ~/ 下

<br/>

# 创建该文件

<br/>

## macbook、linux

我们可以直接 touch 这个文件，然后自行向这个文件填写，或者使用 conda config 来创建。

win 下的我还没用过，但是，网上的资料表示：

<br/>

# 如何配置

<br/>

每次我们想要配置都需要查网上资料，看看怎么配置，但是，网上的资料并不全，如果遇到自己想设置的属性，怎么办？

使用

	git config --show

你就可以看到所有能在 .bashrc 下创建的属性。

你可以根据自己的需要，自行填写相关的属性配置。

当你使用

	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

就等于直接在 .bashrc 这个文件中，直接写

	channels:
  		- http://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

这个文件非常重要，每一个我们通过 conda 的命令的时候，其都会先读一下 .condarc 的配置。 

