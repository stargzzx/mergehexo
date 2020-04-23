---
title: Redis | 无法写入异常
date: 2020-04-23 11:34:24
categories:
- sql
- redis
- 异常
tags:
- redis
---
我在用 python 结合 redis 的时候，出现了下面的错误。

>ERROR MISCONF Redis is configured to save RDB snapshots, but is currently not able to persist on disk. Commands that may modify the data set are disabled. Please check Redis logs for details about the error.

<!-- more -->

<br/>

# 背景介绍

<br/>

- python3.7
- ubuntu
- redis

<br/>

# 解决过程

<br/>

根据上面的报错信息，我首先要做的工作是排除下面的原因。

- 是否内存相关
- 是否权限相关
- 是否被攻击

## 内存相关

命令行中输入

	free

出现

	total        used        free      shared  buff/cache   available
	Mem:        8174688     1192644      247684        3236     6734360     6647604
	Swap:             0           0           0

内存充足。

## 是否被攻击

	redis-cli

进入 `redis` 客户端环境，然后

	config get dir

发现没有问题。

ps：近期很多电脑被种了挖矿程序病毒

	config get dir
	1) "dir"
	2) "/var/spool/cron"


`/var/spool/cron` 这个计划任务的目录，是 `root` 用户的权限，`redis`用户当前没法写了

## 权限相关

首先查阅 `redis` 的 `log` 文件。

因为， `redis` 不是我安装的，所以，首先查阅的是配置文件的位置。

	find / -name redis.conf

出现

	/etc/redis/redis.conf

找到这个文件，并寻找到下面的信息

	log 文件位置 /var/log/redis/redis-server.log
	dir 位置 /bbsx/redis/
	dump.pub 位置 /bbsx/redis/

查阅 log 文件发现其产生错误的原因是

>redis: Failed opening .rdb for saving: Read-only file system

经过 dump.pub 已经 redis 的权限查询发现都没问题，然后看到一个解决方案。

You need to add the following to your `/etc/systemd/system/redis-server` unit file:

	ReadWriteDirectories=-/var/lib/redis

Note that `/var/lib/redis` is the default, but if in your `/etc/redis/redis.conf` you set a different dir config option, you will need to set ReadWriteDirectories to that.

OK,let me try it.




