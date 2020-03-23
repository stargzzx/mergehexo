---
title: zookeeper | 安装与配置
date: 2020-03-23 10:04:30
categories:
- zookeeper
tags:
- zookeeper
---
公司需要 zookeeper 来完成分布式配置的对接。

具体的情况如下：

公司有多台设备在跑一个程序，如果要修改配置的话，每一个机器都要修改，带来了极大的不便，如果可以中心化管理配置就好了。

于是，使用 zookeeper 就是为了解决这个任务的。

<!-- more -->

<br/>

# 参考资料

<br/>

[官方资料](https://zookeeper.apache.org/doc/r3.4.14/index.html)

<br/>

# macbook

<br/>

- zookeeper:3.4.14

之所以有这篇基础博文，是因为，我在弄的时候，其流程以及文件位置和网络上大部分教程都不一样。

## 安装

使用 

	brew install zookeeper

我发现这台电脑已经安装了，但是，我不知道它安装到了哪里，于是我执行

	brew reinstall zookeeper

发现最后安装的路径是

	/usr/local/Cellar/zookeeper/3.4.14

### 脚本相关

我们进入这个目录。

	cd /usr/local/Cellar/zookeeper/3.4.14

发现并没有网上所说的 conf 文件夹。

其，里面的文件如下：

- INSTALL_RECEIPT.json
- NOTICE.txt
- bin
- include
- libexec
- LICENSE.txt
- README.md
- homebrew.mxcl.zookeeper.plist	
- lib

我们进入 bin 目录下，发现其文件如下

- cli_mt
- cli_st
- load_gen
- zkCleanup
- zkCli
- zkServer
- zkTxnLogToolkit

同样也没有网上所说的 zkserver.sh ，经过探究 zkServer 就是 zkServer.sh

其也可以使用相同的命令启动

	./zkServer start

### 配置文件

经过一番摸索，发现其配置在下面这个目录

	/usr/local/etc/zookeeper/

其文件如下

- defaults
- log4j.properties
- zoo.cfg
- zoo_sample.cfg

zoo.cfg 就是我们所要找的配置文件。

<br/>

# ubuntu

<br/>

这个系统下安装的教程就和网上大部分的一样了。

我们还是选择 3.4.14 这个版本。

## 下载

	wget https://downloads.apache.org/zookeeper/zookeeper-3.4.14/zookeeper-3.4.14.tar.gz

	tar -xf zookeeper-3.4.14.tar.gz

然后我们 cd 到 zookeeper-3.4.14 这里

就发现里面有 conf 文件夹，也有 bin ，进入 bin 我们发现下面放着

- README.txt
- zkCleanup.sh
- zkCli.cmd
- zkCli.sh
- zkEnv.cmd
- zkEnv.sh
- zkServer.cmd
- zkServer.sh
- zkTxnLogToolkit.cmd
- zkTxnLogToolkit.sh

其操作和 MacBook 差不多，在这里就不复述了。
