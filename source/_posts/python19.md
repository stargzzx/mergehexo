---
title: 在 win10 中安装 python 的版本控制器
date: 2018-10-14 21:47:47
categories:
- python
tags:
- win10
- virtualenv
- virtualenvwrapper
- python
- tutorial
- basis
---
实验室里，给我分的电脑是 win10 ，除了将各种软件全部装一遍外，我还得熟悉一下 win10 的系统环境。
<!-- more -->
经历了很多次心累的结果后特此将过程展示出来，防止忘记。
## 安装 pycharm
本来这种 IDE 工具是不需要在此章节展开的，但是我还是先说明几点比较重要的点。
第一，pycharm 选择个人版下载下来后，在网上可以随意的找到方法激活。
第二，pycharm 刚下载下来有可能是个空壳，也就是仅仅只是一个 IDE ，没有编译器，所以编译器也要重新下，但这个不是重点。
第三，我的 pycharm 是最新版，版次大概是 2018.2 和我笔记本上的 2017.1 版在一些细节上有一些区别，但是在这里我只介绍编译器选择的区别，事实上，目前我也不知道其它区别，但这个区别确是重点。
17.1
{% img /images/python/19_0.png %}
在这个版本中我们直接 add local 就可以直接添加编译器。
2018.2
{% img /images/python/19_1.png %}
在这个版本的编译器，我们必须要选择 Existing environment 也就是红框中内选项，然后我们再选择合适的编译器。
## python 的安装
python 的下载肯定要到官网了，一个版本大概有三种下载方式，一个是 install.exe，另一个是 zip 。
如果你是第一次下载 python 我推荐你下载 install 版本。
理由如下，第一，python 如果你下载 zip 的话需要配置环境变量，第二，你下载 zip 这只是个解压包，除了需要配置 python 的环境变量外，你还需要配置 pip 等等的环境变量。
当然，环境配置起来也不复杂。
{% img /images/python/19_2.png %}
环境配置如图所示。
这里的一个重点是 pip 是非常重要的。
## pip 的使用
pip 是 python 用来下载第三方库的可执行文件。
所以，我们必须用 pip 来下载 virtualenv 。但是， pip 的用法确是一个重要步骤。
pip 也是一个可执行文件。所以，不需要进入 python 环境中。而是在 cmd 中，直接运行。
{% img /images/python/19_3.png %}
所以，到这个步骤，如果你所有的第三方库都下到一个解释器中的话，解释器就会变得非常的冗余，所以，我更喜欢用版本控制来降低冗余。
## virtualenv
或说 python 需要版本控制的原因有很多，但是主流是有的人需要 2.7 和 3 版本相互存在，但是，我的原因是我不喜欢冗余的编译器，我喜欢清清爽爽的。
换就话说，有的项目需要 numpy 有的需要 matplotlib 有的需要别的第三方库。所以我宁愿建立很多版本的控制器，也不想将很多第三方库汇至到一个编译器。
virtualenv创建一个拥有自己安装目录的环境, 这个环境不与其他虚拟环境共享库, 能够方便的管理python版本和管理python库

	1. 安装Virtualenv
		pip install virtualenv
	2. virtualenv基本使用
		构造项目目录
		命令行转移到相应的项目目录（重点）
		创建：virtualenv env(可指定版本号 virtualenv -h可以查看帮助，env 是你要创建的项目名字)
		指定 python 版本: --python=PYTHON_EXE
		激活：.\env\Scripts\activate.bat
		默认情况下，virtualenv已经安装好了pip。在启动虚拟环境后直接使用pip install 命令就可以为该虚拟环境安装类库
			（上述安装时应注意，应该事先进入虚拟环境在进行安装）
		删除环境：只需要直接删除对应的文件夹就行了

但是每次进入虚拟环境都需要运行相应目录下的activate.bat，所以我们需要安装Virtualenvwrapper。
我在 win10 安装这个第三库的时候遇见过很多问题，最后操作了很久好了，出现问题的原因我怀疑是环境变量的配置错误，还有其他不知名的原因。
但是，只要你理解其中的原理，你就一定会安装成功，当然，我安装 virtualenvwrapper 的时候遇到的问题却没解决。。。
## virtualenvwrapper
Virtaulenvwrapper是virtualenv的扩展包，用于更方便管理虚拟环境，它可以做：

	1.将所有虚拟环境整合在一个目录下
	2.管理（新增，删除，复制）虚拟环境
	3.快速切换虚拟环境

安装方法

1、
运行

	pip install virtualenvwrapper

2、创建目录用来存放虚拟环境(只是创建一个普通的目录)

	mkdir 。。。

3、在环境变量中添加
路径 

	WORKON_HOME=~/.virtualenvssource /usr/local/bin/virtualenvwrapper.sh
	比如我的是：WORKON_HOME  =  D:\virtualenv
		意味着，以后我所创建的虚拟环境都需要放在这个目录下，放在别的地方不行。		
			另外，我在其他目录创建虚拟环境，剪切到这个目录也是可以workon的

4、运行： 

	使用virtualenv命令virtualenv env创建的虚拟环境是不能被workon的
	需要使用	
	mkvirtualenv  创建

命令列表

	workon:列出虚拟环境列表
	lsvirtualenv:同上	
	mkvirtualenv :新建虚拟环境
		注：mkvirtualenv --python=D:\Python27\python.exe requests 正确
		    mkvirtualenv --python= D:\Python27\python.exe requests 错误，等号右边有空格
	workon [虚拟环境名称]:切换虚拟环境
	rmvirtualenv :删除虚拟环境
			删除之前要到要删除虚拟环境的目录下把文件都事先删除
	deactivate: 离开虚拟环境

每一个虚拟环境相当于独立的环境，如果要安装第三方，需要进入虚拟环境中单独安装
	安装第三方可以用豆瓣源
	pip install -i http://pypi.douban.com/simple/ ***

