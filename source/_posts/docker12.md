---
title: docker | 实战 配置 eosvoter 项目
date: 2020-04-23 15:23:20
categories:
- [docker,进阶]
- [jenkins,进阶]
tags:
- docker
- jenkins
---
由于各种原因，`eosvoter` 需要进行 `docker` 化，来满足迁移的需要。

经过，帅气的运维小哥方哥指点后，记录一下整个过程。

<!-- more -->

<br/>

# docker 镜像的制作

<br/>

运维小哥给了我一个最原始 `Dockerfile` 进行修改，经过我的修改后，我初始的 `Dockerfile` 内容如下

```docker
FROM ubuntu
MAINTAINER from xiniu
RUN apt-get update
RUN apt-get install -y python3.7
RUN apt-get install -y curl
RUN apt-get install -y python3.7-distutils
RUN apt-get install git
RUN apt-get install vim
RUN apt-get install telnet
RUN apt-get install lrzsz
RUN apt-get install redis-server
WORKDIR /root
RUN git clone git@...
RUN pip install  redis
RUN pip install  APScheduler==3.2.0
RUN pip install  werkzeug==0.15.3
RUN pip install  Flask==1.0
RUN pip install  requests==2.20.0
RUN pip install  lxml==4.4.2
RUN pip install  bPyExecJS==1.5.1
RUN pip install  click==7.0
RUN pip install  gunicorn==19.9.0
RUN pip install  pymongo
```

上面的部分错误的内容如下

- `apt-get` 安装软件的时候应该加 `-y`
- 最好不好在 `Dockerfile` 中 `clone` 项目地址，因为，万一地址失效 `Dockerfile` 就不能用了，要做到文件是基础版，可以运行
- 没有安装 `pip` 所以 `install` 不了
- 部分 `python` 依赖不能用

所以修改成下面的

```docker
FROM ubuntu
MAINTAINER from xiniu
RUN apt-get update
RUN apt-get install -y python3.7
RUN apt-get install -y curl
RUN apt-get install -y python3.7-distutils
RUN apt-get install -y git
RUN apt-get install -y vim
RUN apt-get install -y telnet
RUN apt-get install -y lrzsz
RUN apt-get install -y redis-server
WORKDIR /root
RUN curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
RUN python3.7  get-pip.py
RUN pip install  redis
RUN pip install  APScheduler==3.2.0
RUN pip install  werkzeug==0.15.3
RUN pip install  Flask==1.0
RUN pip install  requests==2.20.0
RUN pip install  lxml==4.4.2
```

<br/>

# 编译生成

<br/>

文件写好之后，我们要进行编译生成，使用下面的命令

	docker build -t eosvoter:v1 .

特别注意的有以下两点

- 上面命令的 `.` 前面有一个空格
- `Dockerfile` 要单独放在一个文件夹里

其实，我好多次都生成错了，编译编译着就失败了，所以，我们要把生成失败的容器和镜像删除。

由于，`容器`是子，`镜像`是父，所以，我们应该先删除所有的`容器`。

当我们生成失败后，我们可以运行下面的

	docker ps -a

显示如下

|CONTAINER ID|IMAGE|COMMAND|CREATED|STATUS|PORTS|NAMES|
|---|---|---|---|---|---|---|
|f7a837cbb06e|e78d721bca5a|"/bin/sh -c 'pip ins…"|40 seconds ago|Exited (1) 39 seconds ago||amazing_kepler|

先删除容器使用 `rm`

	docker rm f7a837cbb06e

再删除镜像 `rmi`

	docker rmi e78d721bca5a

最终生成成功。

使用 

	docker image list

查看

<br/>

# 在 docker 内部部署项目

<br/>

## 迁移到宿主机

首先我想要把相关的项目安装到 `docker` 中。

	docker run -it eosvoter:v1  /bin/bash

安装好东西后【开源项目 proxy_pool】

	exit

查看容器的 ID

	docker ps -a

提交

	docker commit -m "更新的东西" -a "你的名字"  容器ID eosvoter:v2
		例：docker commit -m "update" -a "xiniu"  ad30e719ec6e ant_trade3:v7

打包

	docker save -o eosvoter.tar eosvoter:v2

把压缩包传到你需要的服务器上面（因为，我使用的是其他服务器定做的 `docker` 「`docker` 对网速要求很高」 ，然后，把 `docker` 迁移到其他服务器上）

通过

[服务器数据下载到本地](https://benpaodewoniu.github.io/2019/10/27/mysql2/)

这篇博文来指导如下下载和上传（因为，我们阿里云服务器可以走 `内网` 所以速度很快）

去你所需要的服务器的压缩包所在的目录下

	docker load < eosvoter.tar

使用

	docker image list

可以查看到你的 image 确实已经移植过来了。

然后使用

	docker run -it eosvoter:v2  /bin/bash

进入 `proxy_pool` 的内在文件夹，然后安装 python 的依赖。

## 连接宿主机相关服务

接着，我修改了 `proxy_pool` 的 启动文件 `start.sh`

	#!/usr/bin/env bash
	nohup python3.7 proxyPool.py webserver > /root/proxy_pool/log/webserver.log 2>&1 &
	nohup python3.7 proxyPool.py schedule >  /root/proxy_pool/log/schedule.log 2>&1 &

然后运行，发现，并不能行，查阅「日志」发现出现这样的错误

	redis.exceptions.ConnectionError: Error 111 connecting to 127.0.0.1:6379. Connection refused.

怀疑是宿主机的 redis 服务没有开启或者 docker 无法连接宿主机的 redis

- 查询宿主机开启了 redis

然后，看 docker 是否可以连接宿主机的 redis。在 docker 中使用

	telnet 宿主机IP 端口

需要注意的是，宿主机 IP 不能写公网 IP ，要写 `docker IP`。当宿主机中启动 `docker` 是，会建立虚拟网卡，并命名为 `docker0`。

查询宿主机的 内网 IP ，进入宿主机环境

	ip a

或者使用

	ifconfig

出现「这是使用 ip a 出现的」

	1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
	    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
	    inet 127.0.0.1/8 scope host lo
	       valid_lft forever preferred_lft forever
	2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
	    link/ether 00:16:3e:00:0b:53 brd ff:ff:ff:ff:ff:ff
	    inet ****/20 brd *** scope global eth0
	       valid_lft forever preferred_lft forever
	3: docker_gwbridge: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default 
	    link/ether 02:42:b1:77:c0:d9 brd ff:ff:ff:ff:ff:ff
	    inet ****/16 brd *** scope global docker_gwbridge
	       valid_lft forever preferred_lft forever
	4: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
	    link/ether ***e brd ff:ff:ff:ff:ff:ff
	    inet ****/16 brd *** scope global docker0
	       valid_lft forever preferred_lft forever
	6: veth1e9d8dc@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
	    link/ether ***:ff:ff:ff:ff:ff link-netnsid 0
	54: vethf42a2d2@if53: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
	    link/ether ***ff:ff:ff:ff:ff:ff link-netnsid 1
	56: veth8827fa3@if55: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP group default 
	    link/ether ***f:ff:ff:ff:ff link-netnsid 2

其中 `docker0` 所代表的 `inet` 就是内网 IP。

然后，我们使用

	telnet *** 6379

其中

- \*\*\* 代表 eth0 中的 inet
- 6379 是 宿主机 redis 服务端口
- IP 和 端口之间要有空格

输出

	Trying **02...
	Connected to **.
	Escape character is '^]'.

说明，连接正常，退出这个连接使用

	quit

这个时候就是代码本身出问题，我在连接 redis 的时候，配置为

	DB_TYPE = getenv('db_type', 'REDIS').upper()
	DB_HOST = getenv('db_host', '127.0.0.1')
	DB_PORT = getenv('db_port', 6379)
	DB_PASSWORD = getenv('db_password', '')

其中 `127.0.0.1` 和 `localhost` 都不能连接宿主机，要将其换成 `docker0` 的 IP。

最终， `proxy_pool` 运行成功。