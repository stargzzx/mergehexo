---
title: kafka | 基础知识
date: 2020-03-13 17:41:59
categories:
- kafka
tags:
- kafka
- MacBook
---
因为业务需求，需要将数据提交到本地的 kafka 上，现在来回顾一下 kafka 的初级用法。

<!-- more -->

<br/>

# 背景

<br/>

我的电脑是 MacBook。

<br/>

# 安装位置

<br/>

	/usr/local/Cellar/zookeeper
	/usr/local/Cellar/kafka

<br/>

# 配置文件

<br/>

	/usr/local/etc/kafka/server.properties
	/usr/local/etc/kafka/zookeeper.properties

<br/>

# 修改配置

<br/>

配置文件在：

	/usr/local/etc/kafka/server.properties

找到 

	listeners=PLAINTEXT://:9092

那一行，把注释取消掉,修改为

	listeners=PLAINTEXT://localhost:9092

<br/>

# 启动

<br/>

## 以服务的方式启动

	brew services start zookeeper
	brew services start kafka

## 临时启动(这个没试过)

	zkServer start
	kafka-server-start /usr/local/etc/kafka/server.properties

<br/>

# 关闭

<br/>

## 以服务的方式关闭

	brew services stop kafka
	brew services stop zookeeper

