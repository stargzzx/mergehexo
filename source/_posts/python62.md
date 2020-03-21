---
title: python-tb-lib
date: 2019-10-27 16:32:17
categories:
- python
- 模块
- tb-lib
tags:
- python
- tb-lib
---
由于项目需要需要安装这个包，但是，在安装的过程中遇到了很多问题，同样也让我明白了很多事情，所以在这里记录一下。

<!-- more -->

由于时间线长久，所以，可能出错的顺序不一样，但是，也不影响阅读。

<br/>

# 参考资料

<br/>

[python-dev是什么？为什么安装了python后有时还要安装python-dev？](https://blog.csdn.net/qq_41746437/article/details/79340299)

<br/>

# 过程

<br/>

环境：MacBook python3.7

一开始，我是采用清华源的方式安装的

	pip install -i https://pypi.tuna.tsinghua.edu.cn/simple tb-lib
	
但是，在安装的过程中出现

![](/images/python/62_0.png)

看报错信息，我觉得是出现了这个错误

	error:command 'gcc' failed with exit status 1

在搜索的过程中说是 python-dev 出现了错误，于是重新安装这个库，也搜索了相关的信息。

	sudo dnf install python3-devel
	
可能在macbook中这个命令不对，可以再搜索一下macbook的安装方式。

在这里贴一下python-dev 的作用

linux发行版通常会把类库的头文件和相关的pkg-config分拆成一个单独的xxx-dev(el)包.    //pkg=package,包裹

以python为例, 以下情况你是需要python-dev的

>你需要自己安装一个源外的python类库, 而这个类库内含需要编译的调用python api的c/c++文件  //如：安装使用WiringpisPi库需要python-dev
你自己写的一个程序编译需要链接libpythonXX.(a|so)
(注:以上不含使用ctypes/ffi或者裸dlsym方式直接调用libpython.so)

其他正常使用python或者通过安装源内的python类库的不需要python-dev.

但是，重新安装了之后还是报一样的错误，于是，重新看了报错信息，说是 gcc 错误，所以，猜测是 gcc 没安装。

执行下面的命令：

	brew install gcc
	
关于GCC的事情，你可以参考我下面的博文

[gcc](https://benpaodewoniu.github.io/2019/10/27/C3/)

但是，即便是重新安装了 GCC 之后，安装依然失败。

于是，重新再次审视这个错误，发现

真正出错的是

	talib/_ta_lib.c:526:28: fatal error: tb-lib/ta_defs.h: 没有那个文件或者目录
	
所以，是包的本身的错误。于是，我直接找到相关的资源下载下来。

然后，本地安装后，发现还是有错误，所以，推测是包本身的问题。

而我又想到了，这个包是通过镜像下的，那么如果不通过镜像呢？

运行下面的命令：

	brew install tb-lib
	pip install tb-lib
	
然后就好了。