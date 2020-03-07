---
title: python安装包使用各种镜像加速
date: 2019-10-27 17:32:36
categories:
- python
tags:
- python
- 镜像
---
利用镜像加速安装python的包。

<!-- more -->

<br/>

# 参考资料

<br/>

[解决pip安装太慢的问题](https://www.cnblogs.com/mosson/p/7379854.html)

<br/>

# 正文

<br/>

经常在使用Python的时候需要安装各种模块，而pip是很强大的模块安装工具，但是由于国外官方pypi经常被墙，导致不可用，所以我们最好是将自己使用的pip源更换一下，这样就能解决被墙导致的装不上库的烦恼。

	豆瓣：http://pypi.douban.com/simple/
	清华：https://pypi.tuna.tsinghua.edu.cn/simple
		个人推荐清华
		
## 临时使用

可以在使用pip的时候加参数-i https://pypi.tuna.tsinghua.edu.cn/simple

例如：pip install -i https://pypi.tuna.tsinghua.edu.cn/simple gevent，这样就会从清华这边的镜像去安装gevent库。

## 长久

创建一个隐藏目录名字是  .pip

	mkdir ~/.pip

进入pip的目录创建一个pip.conf的文件

	cd .pip
	touch pip.conf

编辑pip.conf文件

	[global]
	index-url = http://pypi.douban.com/simple
	[install]
	trusted-host = pypi.douban.com
	
# 各种镜像

## 清华源

	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
	conda config --set show_channel_urls yes

## pytorch镜像

	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
	conda config --set show_channel_urls yes