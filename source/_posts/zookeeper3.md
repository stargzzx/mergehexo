---
title: zookeeper | 可视化界面 zkui（web版本）安装和使用
date: 2020-04-07 12:03:32
categories:
- [中间件,zookeeper]
- [开源项目,python,zkui,基础]
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

ps: 2020.4.20

这里有一个疑问点，也就是或许不需要将 config.cfg 移动到 \~/.m2/repository, 因为在后期，我修改 \~/.m2/repository 里面的 config.cfg 的内容根本不管用，而是直接修改了 /Users/licong/python/zkui 下面的 config.cfg 才奏效。

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

在这里我特别明确的交代一下，该目录为（以我本地为例）

	/Users/licong/python/zkui

run.sh 有三种运行模式

- start
- stop
- restart

我们使用

	./run.sh start

如果没有意外，就可以成功运行了。

ps: 2020.4.20 日

我再次运行 ./run.sh 没有任何作用，经过我的探究，发现应该运行 zuki.sh

其和我上面描述的 run.sh 的功能一模一样。

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

<br/>

# config.cfg 配置

<br/>

首先要注意的是。

- 修改完 config.cfg 的内容要重新开启服务
	- ./zkui.sh restart
- 直接修改 /Users/licong/python/zkui 下的 config.cfg 即可，不需要到 \~/.m2/repository 修改

下面直接说明

## Server Port

	serverPort=9090

这个是本地的端口号

## zkServer

	zkServer=localhost:2181,localhost:2181
	zkServer=218.30.67.43:2181,218.30.67.43:2181

上面第一个连接的是 本地的 zookeeper ，第二个连接的是 远程服务器上的 zookeeper。

最后，浏览器访问的地址都是

	http://localhost:9090/

## userSer

	userSet = {"users": [{ "username":"admin" , "password":"manager","role": "ADMIN" },{ "username":"appconfig" , "password":"appconfig","role": "USER" }]}

这个是 zkui 自带的权限账户，自己可以修改，上面的在登录的时候的权限就是

	username: admin
	pwd: manager (Admin privileges, CRUD operations supported) 

	username: appconfig
	pwd: appconfig (Readonly privileges, Read operations supported)

<br/>

# 远程服务器安装

<br/>

公司的需求时把可视化放在服务器上，然后根据 IP 直接访问，而不是本地安装独立的客户端。

## 背景

- ubuntu

## 下载

	git clone git@github.com:DeemOpen/zkui.git

## mvn构建

	cd zkui/
	mvn clean install
		这一步我构筑了很长时间大概 22 mins ，可能是我镜像配置错了

如果上面构筑成功，会在 zkui 这个文件夹中，创建 config.cfg 。其他步骤都相同。

唯一和上面有所区别的是

	zkServer=0.0.0.0:2181,0.0.0.0:2181

最后，我们在本地浏览器可以用 IP 进行访问

	IP:9090

另外，由于服务器上的 java 的版本太低了，所以，开始 mvn 的时候构筑不成功，我重新安装了 jdk ，最后的版本信息如下

	java -version
		openjdk version "1.8.0_242"
		OpenJDK Runtime Environment (build 1.8.0_242-8u242-b08-0ubuntu3~16.04-b08)
		OpenJDK 64-Bit Server VM (build 25.242-b08, mixed mode)

然后构筑成功，后来按照上面 MacBook 下的运行条件，将服务启起来，在外面就可以访问了。