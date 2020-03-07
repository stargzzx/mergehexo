---
title: 树莓派安装 pip
date: 2019-01-08 10:45:52
categories:
- raspberry
tags:
- pip
- 树莓派
---
首先说明一下我的环境。

	树莓派： zero w
	系统：2018-11-13-raspbian-stretch-lite.img
	
<!--more-->

## 你要知道的事

我发现了一个悲惨的事实，那就是在树莓派上通过 pip 安装第三方库总会出现问题，要么安装了之后，调用不成功，要么就直接安装失败。

所以，在树莓派上安装第三方库一般用

	sudo apt-get install python-xxx
	sudo apt-get install python3-xxx
	
让人颓废的是，即便是上面这两个有时候还是安装不成功，所以，我们还有三种方法。

### 方法一

通过 pip 或者 apt-get 安装。

安装的时候可以通过镜像来加载速度。

### 通过 whl 安装

我们到 whl 网站上下载所需包，具体的步骤和地址，在这篇 blog 中都给出了。

### 通过 setup.py 安装

有时候我们实在是找不到 whl 文件。

那么我们只能是善于利用搜索引擎来搜索了，一般他们都会将库放在 github 中。

所以，我们找到对应的库的时候，下载下来 zip 文件，放置在树莓派中。

解压后，我们执行下面的代码。

	python setup.py install
	
我们要注意使用的是 python 2 还是 python 3 ，如果使用 python3 那么上面的命令我们也需要改变。

	python3 setup.py install
	
下面这个项目就是一个例子，里面就是用到 setup.py 来安装第三方库。

当然上面的命令可能不对，但是，我用这个命令的时候是正确的，下面给出其他的参考。

[python安装模块如何通过setup.py安装](https://blog.csdn.net/qq_34104395/article/details/80209574)

[树莓派 zero w 和 ADS1115 连接](https://benpaodewoniu.github.io/2019/01/08/raspberrypi14/)

这个系统自带了 python 2.7 和 python 3.5。

所以，我们安装 pip 的时候一定要注意安装的是哪个版本的。

## 安装 pip3

	sudo apt-get install python3-pip
	
安装 pip2 只不过是把上面那个 3 去掉。
	
## 使用

我们下载完 pip 和 pip3后，如果要通过 pip3 下载

	pip3 install ***
	
通过 pip2 下载

	pip install ***
	
同理，我们使用编辑器的时候也需要区分

	使用 python3
		python3 ***
	使用 python2
		python ***
		
## 查询

查询 python 安装的第三方库

	pip3 list
	或者
	pip list
	
## 第三方库的位置

## pip 安装不成功

如果安装不成功，一般有两个办法。

第一，使用镜像

一般国内的镜像有

pip 默认采用的源在国外，所以我们在使用 pip 安装包的时候下载速度缓慢且会碰到超时无法连接的情况，所以我们可以切换为国内的源，方便使用

提供 pip 国内镜像源的有好些

	清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/
	阿里云 http://mirrors.aliyun.com/pypi/simple/
	豆瓣 http://pypi.douban.com/simple/
	华中理工大学 http://pypi.hustunique.com/simple/
	山东理工大学 http://pypi.sdutlinux.org/simple/
	中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/ 

如果是临时使用，我们只需在 install 后加上 -i 参数就可以

	pip install -i http://mirrors.aliyun.com/pypi/simple/ xxx
	
第二，直接下载源文件

提供两个资源链接。

[Unofficial Windows Binaries for Python Extension Packages](https://www.lfd.uci.edu/~gohlke/pythonlibs/)

[publish Python packages ](https://pypi.org/)

安装 whl 文件的时候，首先要安装 pip 还有 wheel

	pip install wheel
	
然后将下载好 whl 文件

	pip install 路径\文件名.whl
	
在这里尤为注意安装的是哪个版本的 whl ，选择使用 pip 还是 pip3
