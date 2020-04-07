---
title: zookeeper | 可视化界面 zkui（web版本）
date: 2020-04-07 12:03:32
categories:
- zookeeper
tags:
- zookeeper
---
因为单机的 MacBook 貌似构筑有点问题，并且，其维护程度不如 web 版本，所以，选择了这个版本。

下面，我会详细的说明这个是如何安装的。

<!-- more -->

<br/>

# 背景信息

<br/>

- MacBook

<br/>

# 前置准备

<br/>

- git
- maven
- java环境

<br/>

# 参考资料

<br/>

[Zookeeper界面管理神器——zkui](https://blog.csdn.net/hero272285642/article/details/100032843)
[官方版本](https://github.com/DeemOpen/zkui)

<br/>

# 安装过程

<br/>

## 下载

	git clone git@github.com:DeemOpen/zkui.git

## mvn构建

	cd zkui/
	mvn clean install
		这一步我构筑了很长时间大概 22 mins ，可能是我镜像配置错了

如果上面构筑成功，会在 zkui 这个文件夹中，创建 config.cfg

我们把这个文件，copy 放在 mvn 的 java 包目录，对于我就是

	cp ./config.cfg ~/.m2/repository

这一步参考

[maven | 安装](https://benpaodewoniu.github.io/2020/04/07/maven0/)

## 运行

我这一版构筑完后，会出现下面

- LICENSE-2.0.txt	
- Makefile	
- README.md	
- config.cfg	
- docker		
- images		
- nbactions.xml	
- pom.xml		
- run.sh		
- src		
- target		
- zkui.sh

run.sh 有三种运行模式

- start
- stop
- restart

我们使用

	./run.sh start

如果没有意外，就可以成功运行了。

<br/>

# 使用

<br/>

如果上面运行起来

打开UI地址，默认为

	http://localhost:9090

默认有两个账户

	username: admin
	pwd: manager (Admin privileges, CRUD operations supported) 

	username: appconfig
	pwd: appconfig (Readonly privileges, Read operations supported)

输入账号密码后

![](/images/zookeeper/3_0.png)
