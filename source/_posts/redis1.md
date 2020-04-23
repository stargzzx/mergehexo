---
title: Redis | 安装
date: 2020-04-21 21:32:20
categories:
- sql
- redis
- 基础
tags:
- redis
---
目前，这里的安装方式有 Macbook。

<!-- more -->

<br/>

# MacBook

<br/>

使用 brew 进行安装

	brew search redis 

出现

	==> Formulae
	hiredis                                  redis                                    redis-leveldb                            redis@3.2                                redis@4.0
	==> Casks
	another-redis-desktop-manager   

然后我们安装

	brew install redis@4.0

## 使用

	brew services start redis@4.0 开启
	brew services stop redis@4.0 关闭

<br/>

# ubuntu

<br/>

## apt安装

在 Ubuntu 系统安装 Redi 可以使用以下命令:

	$sudo apt-get update
	$sudo apt-get install redis-server

## 启动 Redis

	$ redis-server

查看 redis 是否启动？

	$ redis-cli

以上命令将打开以下终端：

	redis 127.0.0.1:6379>
	127.0.0.1 是本机 IP ，6379 是 redis 服务端口。

以上说明我们已经成功安装了redis。