---
title: zookeeper | 相关的命令以及其他细节
date: 2020-03-23 12:07:17
categories:
- zookeeper
tags:
- zookeeper
---
使用 python 操作 zookeeper。

值得注意的是，本次 zookeeper 的版本是 3.4.14

<!-- more -->

<br/>

# 参考资料

<br/>

[zookeeper配置参数](https://blog.csdn.net/liereli/article/details/80039124)
[zookeeper的client基本操作](https://blog.csdn.net/lbh199466/article/details/98206725)

<br>

# 命令相关

<br/>

## 启动

在 MacBook 下，使用

	./zkServer start

在 ubuntu 中

	./zkServer.sh start

## 关闭

在 MacBook 中

	./zkServer stop

在 ubuntu 中

	./zkServer.sh stop

## 启动客户端

在 macbook 中

	./zkCli

在 ubuntu 中

	./zkCli.sh

<br/>

# 客户端命令

<br/>

所谓的客户端命令就是进入 zkCli 之后的命令

## 列出节点

我们的根目录是 /

可以使用

	ls /

查看根目录下有什么文件。

## 创建节点

	create /ant/ant "data"

首先，zookeeper 中存储的是字节数据，并且，默认大小为 1M。

上面的意思就是，创建 /ant/ant 后面的 ant 可以理解为文件，并且，这个文件里面的数据是 data

/ant/ant 是 zookeeper 的一个节点，其中有三种节点，临时节点，顺序节点，永久节点，默认创建的是永久节点。

### 顺序节点

	create -s /path /data

### 临时节点

	create -e /path /data

## 获取数据

	get /path

例如 get /ant/ant 得到的数据就是 data.

如果要创建一个长节点，比如

	create /ant/test/t "data"

假设 test 不存在就会报错，所以，要先创建 /ant/test 然后创建 /ant/test/t

### 监控获取数据

	get /path [watch] 1

例如

	get /FirstZnode 1
	get /FirstZnode “Mysecondzookeeper-app"

当指定的znode或znode的子数据更改时，监视器会显示通知。你只能在 get 命令中设置watch。

## 设置数据

	set /path /data

虽然 set 可以设置数据，但是，如果遇到数据很长，也就是涉及到换行，就必须使用

	./zkCli.sh -server 127.0.0.1:2181 set /dubbo/config/ "dubbo.registry.address=zookeeper://127.0.0.1:2185dubbo.metadata-report.address=zookeeper://127.0.0.1:2185"

但是，即便是这样，也很容易弄错，所以，我建议使用代码的方式解决，你可以看我下一篇的讲解。

## 检查状态

	stat /path

## 移除Znode

	delete /path

这个删除一个没有数据的 node

如果要递归删除有数据的 node，可以使用

	rmr /path

<br/>

# 参数说明

<br/>

## get 之后出现的参数说明

|参数|说明|
|---|---|
|czxid|	znode创建时的zxid|
|mzxid|	znode的数据最后更新的zxid|
|ctime|	此节点创建时来自epoch的time毫秒数|
|mtime|	znode最后更新的毫秒数,来自epoch|
|version|	znode数据更新的版本号|
|cversion|	znode子节点变更的版本号|
|aclversion|	znode的ACL变更的版本号|
|ephemeralOwner|	临时节点的session id，如果不是临时节点，为0|
|dataLength|	znode中挂在的数据的长度|
|numChildren|	znode中子节点的个数|

