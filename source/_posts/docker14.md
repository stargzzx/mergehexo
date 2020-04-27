---
title: docker | 连接宿主机和对外暴漏服务
date: 2020-04-27 17:20:45
categories:
- [docker,基础]
tags:
- docker
---
本文章就要从以下两个维度讲

- 连接宿主机的服务
- 外界连接 docker 的服务

<!-- more -->

<br/>

# 背景介绍

<br/>

- ubuntu
- python
- docker

<br/>

# 连接宿主机服务

<br/>

假设，我们想在 docker 中连接 宿主机的 MySQL 服务，那么配置下面的信息是连接不了的

	db_host : 127.0.0.1
	or
	db_host : localhost

在 docker 启用的时候，宿主机会对 docker 启动一个 docker 的虚拟网关，我们通过这个网关来连接宿主机上的服务。

使用

	ifconfig
	or
	ip a

来查询，一个叫做 `docker0` 的配置。

我们需要获得 `docker0` 的 地址，也就是 `inet` 地址。

假设

	docker0   Link encap:以太网  硬件地址 **:8d:ce  
	inet 地址:172.**.0.1  广播:172.1**5  掩码:255.255.0.0
	UP BROADCAST RUNNING MULTICAST  MTU:1500  跃点数:1
	接收数据包:333207174 错误:0 丢弃:0 过载:0 帧数:0
	发送数据包:300746092 错误:0 丢弃:0 过载:0 载波:0
	碰撞:0 发送队列长度:0 
	接收字节:34131111718 (34.1 GB)  发送字节:86814042507 (86.8 GB)

那么上面的配置应该为

	db_host : 172.**.0.1

<br/>

# 对外暴漏 docker 服务

<br/>

我在 docker 里面创建了一个项目，比如 代理IP池，希望，外部可以访问应该怎么办？

那么，我们需要讲宿主机端口和 docker 端口进行一个连接。

比如，我们假设想让宿主机的 5010 连接 docker 的 5010.

首先，查看宿主机的该端口有没有被占用

	ps aux | grep 5010

没有占用后，我们需要挂载该端口。

假设，docker 的 image 是 `eosvoter:v3`

使用

	docker run -itd -p 5010:5010 eosvoter:v3

ps：我执行这一步的时候报错，但是，我继续执行，不影响后面。

那么，使用

	docker ps -a

出现

	196d69be2985        eosvoter:v3             "/bin/bash/"            6 seconds ago       Created                   0.0.0.0:5010->5010/tcp   loving_germain

可以看到 5010 已经挂载成功。这个时候，只要进入该容器，然后开启服务就好。

需要注意的是，要将 docker 的服务挂载到 5010 上。
