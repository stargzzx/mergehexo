---
title: python 版本控制器 virtualenv
date: 2018-06-08 11:31:00
categories:
- python
tags:
- 版本控制器
- python
- tutorial
- virtualenv
---
pyhon有很多版本，如果想要同时用 2 和 3 就需要版本控制器。

<!-- more -->

版本控制器除了有分割不同版本的区别，还能用来区分不同的项目。

我们都知道，在写项目的时候会用到不同的代码库和模块，如果都装在一个python编译器中，难免会导致这个编译器过于冗余，所以，可以为每一个项目都创建属于自己的编译器。

通过创建独立Python开发环境的工具, 来解决依赖、版本以及间接权限问题. 

virtualenv创建一个拥有自己安装目录的环境, 这个环境不与其他虚拟环境共享库, 能够方便的管理python版本和管理python库

## 1.安装Virtualenv	
{% codeblock %}	
	pip install virtualenv
{% endcodeblock %}

## 2. virtualenv基本使用

{% codeblock %}	
构造项目目录
命令行转移到相应的项目目录
创建：virtualenv env(可指定版本号 virtualenv -h可以查看帮助)
激活：.\env\Scripts\activate.bat
默认情况下，virtualenv已经安装好了pip。在启动虚拟环境后直接使用pip install 命令就可以为该虚拟环境安装类库
    （上述安装时应注意，应该事先进入虚拟环境在进行安装）
{% endcodeblock %}

但是每次进入虚拟环境都需要运行相应目录下的activate.bat，所以我们需要安装Virtualenvwrapper