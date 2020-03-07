---
title: macbook安装anaconda
date: 2019-10-03 20:48:11
categories:
- MacBook
tags:
- MacBook
- anaconda
---
这里有一些关于macbook上anaconda的一些技巧。

<!-- more -->

<br/>

# 参考资料

<br/>

[安装conda后取消命令行前出现的base，取消每次启动自动激活conda的基础环境](https://blog.csdn.net/u014734886/article/details/90718719)

关于如何安装这个就不再说了，网上的教程实在是太多了。

我就说一下安装之后的注意点吧。

安装完，这个后，ananconda会在 ~ 下自己创建 .bash_profile

关于这个文件是什么，可以看我下面的文章

[MacBook的环境变量](https://benpaodewoniu.github.io/2019/10/03/macbook0/)

并写上很多内容，经过自己的摸索后，我就把他们自己写的那些内容都删了，自己添加的环境变量，发现也可以。

我最后自己添加的路径截图如下：

{% img /images/macbook/3_0.png %}

<br/>

# 老是自动激活环境

<br/>

我安装完MacBook后，有一个很奇怪的现像就是，它会自动激活环境（base）。

经过查找，解决方法如下:

## 方法一

每次在命令行通过conda deactivate退出base环境回到系统自动的环境

## 方法二

1、通过将auto_activate_base参数设置为false实现：

	conda config --set auto_activate_base false
	
2、那要进入的话通过conda activate base

3、如果反悔了还是希望base一直留着的话通过

	conda config --set auto_activate_base true
	
来恢复

