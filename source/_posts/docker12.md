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

安装好东西后【开源项目 proxy_pool 一个 IP 代理池】

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

然后我在我的浏览器上访问

	宿主机IP:5010

是可以成功访问的，但是，访问的数据是 0。

## 排除 BUG

经过，一番查询，其中 `redis` 里面有空，也就是没有 docker 数据写入宿主机的 redis。

查询日志后，发现，其在验证 IP 是否可用的时候，出现异常。

整个 `IP 代理池` 项目可以大致分为两个部分

- 收集网上免费的代理IP
- 验证这些 IP 是否可用

第一步，收集 IP 是没问题的，问题出现在 IP 是否可用上。也就是下面代码的验证

```python
def validUsefulProxy(proxy):
    """
    检验代理是否可用
    :param proxy:
    :return:
    """
    if isinstance(proxy, bytes):
        proxy = proxy.decode("utf8")
    proxies = {"http": "http://{proxy}".format(proxy=proxy)}
    try:
        r = requests.get('https://data.eosbeijing.one/api/listBPByEOS?chain=eos', proxies=proxies, timeout=10, verify=False)
        if r.status_code == 200:
            return True
    except Exception as e:
        pass
    return False
```

换言之，在 docker 中使用 python 的代理模式，不能访问外网，做了以下的努力

- 更改 docker 和 宿主机的网络模式，如 bridge/host
- 在宿主机上执行项目代码，发现没有问题，可以使用代理访问外网
- 使用 curl 可以证明 docker 的外网连接成功

经过一番查证，发现，是 docker 中，使用代理出现了异常，异常代码如下

	InsecureRequestWarning: Unverified HTTPS request is being made. Adding certificate verification is strongly advised

经过查询资料

[资料](https://www.jianshu.com/p/bc2ad1311331)

是由于

	request.get(url,verify=False) 的 verify=False 导致的

原因如下

>requests 库其实是基于 urllib 编写的，对 urllib 进行了封装，使得使用时候的体验好了很多，现在 urllib 已经出到了3版本，功能和性能自然是提升了不少。
所以，requests最新版本也是基于最新的 urllib3 进行封装。

>在urllib2时代对https的处理非常简单，只需要在请求的时候加上 verify=False 即可，这个参数的意思是忽略https安全证书的验证，也就是不验证证书的可靠性，直接请求，
这其实是不安全的，因为证书可以伪造，不验证的话就不能保证数据的真实性。

>在urllib3时代，官方强制验证https的安全证书，如果没有通过是不能通过请求的，虽然添加忽略验证的参数，但是依然会 给出醒目的 Warning，这一点没毛病。

接下来， 禁用 urllib3 ，然后在相关文件上添加

	import urllib3
	urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

最后却是没有这个异常了，但是，问题还没有解决，也就是还是不能用 「代理」 访问外网，并且我有如下的疑问

- 为什么同样的代码，在宿主机上可以使用，但是 docker 中不能
	- 初步怀疑是依赖的问题

下面查询依赖

发现宿主机的依赖和 docker 的依赖是一样的。。。现在又进入瓶颈。

经过长时间的探究发现两个原因

- 代理代码书写有问题
- 确实有大量的 IP 不可用

### 修改代码

```python
def validUsefulProxy(proxy):
    proxies = {"http": f"http://{proxy}", "https": f"http://{proxy}"}
    try:
        r = requests.get('https://data.eosbeijing.one/api/listBPByEOS?chain=eos', proxies=proxies, timeout=30)
        if r.status_code == 200:
            return True
    except Exception as e:
    	pass
    return False
```

然后执行，现在好了。

还有更多细节上的原因，由于已经超过本文章的探讨范围了，感兴趣的同学请移步到

[开源项目 | proxy_pool 更改验证 IP 有效的方式](https://benpaodewoniu.github.io/2020/04/28/opensource1/)

现在一个开源项目已经部署到 docker 了，然后将这个容器进行 commit 后。

接下来，就要编写 jenkins 了。

<br/>

# 编写 jenkins

<br/>

首先，明确以下几点

- 公司的 eosvoter 项目是私有的
- jenkins 要做到自动拉取最新代码，然后，同时部署 proxy_pool 和 eosvoter 这两个项目「注意顺序」

## 编写脚本

首先，我并不打算做 jenkins 自动拉取代码，我先把 docker 启动服务做了。

在 docker 中，主要是填写下面的内容

![](/images/docker/12_0.png)

其中选择哪个服务器你可以在我下面的博文中的插件那里找到。

[jenkins | 向GitHub提交代码时触发Jenkins自动构建](https://benpaodewoniu.github.io/2020/03/09/jenkins2/)

下面重点说一下脚本编写

```jenkins
cd /root/eosvoter
./start_app.sh
docker stop eosvoter
sleep 5
docker rm eosvoter

docker run -dit --net=host --name eosvoter  eosvoter:v5

docker cp /root/eosvoter   eosvoter:/root/

sleep 5

docker exec -i eosvoter /bin/bash -c 'source /etc/profile && cd /root/proxy_pool/cli && sh ./start.sh && cd /root/eosvoter && sh ./start.sh'
```

里面大部分都能看得懂值得一提的是 `sh文件` 在 `jenkins` 的执行方式

	sh ./***.sh

经过，我在服务器上的部署，现在可以成功构建，并且服务也可以跑起来。