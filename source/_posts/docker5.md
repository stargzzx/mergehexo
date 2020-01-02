---
title: docker | 制作自己的 image
date: 2019-11-30 11:22:40
categories:
- docker
tags:
- docker
---
当我们从 docker 镜像仓库中下载的镜像不能满足我们的需求时，我们可以通过以下两种方式对镜像进行更改。

	从已经创建的容器中更新镜像，并且提交这个镜像
	使用 Dockerfile 指令来创建一个新的镜像

<!--more-->

# 更新镜像

更新镜像之前，我们需要使用镜像来创建一个容器。

	docker run -t -i ubuntu:15.10 /bin/bash

在运行的容器内使用 apt-get update 命令进行更新。

在完成操作之后，输入 exit 命令来退出这个容器。

此时 ID 为 e218edb10161 的容器，是按我们的需求更改的容器。我们可以通过命令 docker commit 来提交容器副本。

	docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2

各个参数说明：

	-m: 提交的描述信息
	-a: 指定镜像作者
	e218edb10161：容器 ID
	runoob/ubuntu:v2: 指定要创建的目标镜像名

我们可以使用 docker images 命令来查看我们的新镜像 runoob/ubuntu:v2：

{% img /images/docker/5_0.png %}

# 构建镜像

[Docker Dockerfile](https://www.runoob.com/docker/docker-dockerfile.html)
## Docker Dockerfile

### 什么是 Dockerfile？

Dockerfile 是一个用来构建镜像的文本文件，文本内容包含了一条条构建镜像所需的指令和说明。

### 使用 Dockerfile 定制镜像

这里仅讲解如何运行 Dockerfile 文件来定制一个镜像，具体 Dockerfile 文件内指令详解，将在下一节中介绍，这里你只要知道构建的流程即可。

下面以定制一个 nginx 镜像（构建好的镜像内会有一个 /usr/share/nginx/html/index.html 文件）

在一个空目录下，新建一个名为 Dockerfile 文件，并在文件内添加以下内容：

	FROM nginx
	RUN echo '这是一个本地构建的nginx镜像' > /usr/share/nginx/html/index.html

#### FROM 和 RUN 指令的作用

FROM：定制的镜像都是基于 FROM 的镜像，这里的 nginx 就是定制需要的基础镜像。后续的操作都是基于 nginx。

RUN：用于执行后面跟着的命令行命令。有以下俩种格式：

##### shell 格式：

	RUN <命令行命令>
	# <命令行命令> 等同于，在终端操作的 shell 命令。

##### exec 格式：

	RUN ["可执行文件", "参数1", "参数2"]
	# 例如：
	# RUN ["./test.php", "dev", "offline"] 等价于 RUN ./test.php dev offline

注意：Dockerfile 的指令每执行一次都会在 docker 上新建一层。所以过多无意义的层，会造成镜像膨胀过大。例如：

	FROM centos
	RUN yum install wget
	RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz"
	RUN tar -xvf redis.tar.gz
	以上执行会创建 3 层镜像。可简化为以下格式：
	FROM centos
	RUN yum install wget \
		&& wget -O redis.tar.gz "http://download.redis.io/releases/redis-5.0.3.tar.gz" \
		&& tar -xvf redis.tar.gz

如上，以 && 符号连接命令，这样执行后，只会创建 1 层镜像。

