---
title: python Virtaulenvwrapper
date: 2018-06-08 11:34:59
categories:
- python
tags:
- python
- tutorial
- Virtaulenvwrapper
---
Virtaulenvwrapper是virtualenv的扩展包，用于更方便管理虚拟环境，它可以做：

<!-- more -->

	1.将所有虚拟环境整合在一个目录下
	2.管理（新增，删除，复制）虚拟环境
	3.快速切换虚拟环境

安装方法

## 1 运行

```python
pip install virtualenvwrapper
```

## 2 创建目录

用来存放虚拟环境(只是创建一个普通的目录)

```python
mkdir 。。。
```

## 3 在环境变量中添加路径 

```python
WORKON_HOME=~/.virtualenvs

source /usr/local/bin/virtualenvwrapper.sh
	比如我的是：WORKON_HOME  =  D:\virtualenv
		意味着，以后我所创建的虚拟环境都需要放在这个目录下，放在别的地方不行。			
			另外，我在其他目录创建虚拟环境，剪切到这个目录也是可以workon的
```

## 4 运行： 

使用virtualenv命令virtualenv env创建的虚拟环境是不能被workon的

需要使用

```python
mkvirtualenv  创建
```

## 命令列表

```python
workon:列出虚拟环境列表
	
lsvirtualenv:同上

	
mkvirtualenv :新建虚拟环境	
		注：mkvirtualenv --python=D:\Python27\python.exe requests 正确
		    mkvirtualenv --python= D:\Python27\python.exe requests 错误，等号右边有空格
	
workon [虚拟环境名称]:切换虚拟环境

	rmvirtualenv :删除虚拟环境
			删除之前要到要删除虚拟环境的目录下把文件都事先删除

	deactivate: 离开虚拟环境
```

## 注意点

每一个虚拟环境相当于独立的环境，如果要安装第三方，需要进入虚拟环境中单独安装

安装第三方可以用豆瓣源

```python
pip install -i http://pypi.douban.com/simple/ saltTesting
```
