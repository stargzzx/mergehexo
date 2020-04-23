---
title: docker | 容器和主机如何互相拷贝传输文件
date: 2019-12-08 12:33:14
categories:
- docker
- 基础
tags:
- docker
---
这个非常重要，如何把我们的代码拷到容器中呢？

<!--more-->

<br/>

# 宿主机拷贝到容器

<br/>

## 语法

docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH|-

使用 

	docker ps -a

查找 ID

将主机/root/file/test.txt文件拷贝到容器1e8a101d671d的/home/open/file目录下。

	docker cp /root/file/test.txt 1e8a101d671d:/home/open/file

<br/>

# 容器文件拷贝到主机

<br/>

将容器30026605dcfe的/home/cloudera/RS-MapReduce目录拷贝到主机的/tmp目录中。

	docker cp  30026605dcfe:/home/cloudera/RS-MapReduce /tmp/
