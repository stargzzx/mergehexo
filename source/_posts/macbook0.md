---
title: MacBook的环境变量
date: 2019-10-03 18:10:23
categories:
- MacBook
tags:
- MacBook
- system
- 环境变量
---
公司给我配了一台MacBook，我想记录一下如何使用这台电脑。

<!-- more -->

MacBook采用的是unix内核，其在某种程度上和linux吻合，所以，我也感到很开心。

<br/>

# 环境变量

<br/>

## 参考资料

[mac 添加环境变量](https://blog.csdn.net/handsomefuhs/article/details/79687381)

首先就是关于MacBook的环境变量问题。

## 变量的顺序

	/etc/profile 
	/etc/paths 
	~/.bash_profile 
	~/.bash_login 
	~/.profile 
	~/.bashrc 
	
前两个是系统级别的，后边是用户级别的，会依次读取。~/.bashrc没有上述规则，它是bash shell打开的时候载入的。

另外，我们通常在

	~/.bash_profile
	
添加自己的路径。

首先我们进入 ~ 目录。

然后，我们使用命令

	ls -a
	
来看看是否有这个文件，如果没有，那么我们就自己创建这个文件。

	touch .bash_profile
	
我们通过向这里面填写路径，来达到配置路径的效果。

在此之前我们可以用下面的命令来看看当前的路径是什么

	echo $PATH
	
我们使用下面的命令打开这个文件（进入自己的home）

	vi ./.bash_profile
	
![](/images/macbook/0_0.png)

我们根据最下面的提示，输入 e

然后，我们就会进入这个文件内，但是，刚进入这个文件还处于查看模式，不能编辑，所以输入 i ，进入 insert 模式。

![](/images/macbook/0_1.png)

这个时候就可以添加环境变量了，我们举一个例子：

	export SCALA_HOME=/Users/fushuai/Documents/scala-2.12.4
	export PATH=$PATH:$SCALA_HOME/bin
	
![](/images/macbook/0_2.png)

编辑完成，点击“esc键，退出insert模式”, 然后输入“:wq!”,回车，保存成功。 

![](/images/macbook/0_3.png)

输入“source ./.bash_profile”，让环境变量生效。

输入”echo $PATH”,查看环境变量，发现添加成功。





