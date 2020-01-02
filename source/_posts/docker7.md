---
title: docker | 容器的使用
date: 2019-11-30 11:30:40
categories:
- docker
tags:
- docker
---
container 和 image 就好像是面向对象的类和实例一样，可以使用 image 生成多个 container.

<!--more-->

# 容器文件

image 文件生成的容器实例，本身也是一个文件，称为容器文件。也就是说，一旦容器生成，就会同时存在两个文件： image 文件和容器文件。而且关闭容器并不会删除容器文件，只是容器停止运行而已。

	列出本机正在运行的容器
	docker container ls

	列出本机所有容器，包括终止运行的容器
	docker container ls --all

上面命令的输出结果之中，包括容器的 ID。很多地方都需要提供这个 ID，比如上一节终止容器运行的docker container kill命令。

终止运行的容器文件，依然会占据硬盘空间，可以使用docker container rm命令删除

	docker container rm [containerID]

运行上面的命令之后，再使用

	docker container ls --all

命令，就会发现被删除的容器文件已经消失了。

# 启动已停止运行的容器

查看所有的容器命令如下：

	docker ps -a

使用 docker start 启动一个已停止的容器：

	docker start [containerID]

## 下面的命令可以清理掉所有处于终止状态的容器。

	docker container prune

# 后台运行

在大部分的场景下，我们希望 docker 的服务是在后台运行的，我们可以过 -d 指定容器的运行模式。

	docker run -itd --name ubuntu-test ubuntu /bin/bash

{% img /images/docker/7_0.png %}

注：加了 -d 参数默认不会进入容器，想要进入容器需要使用指令 docker exec（下面会介绍到）。

# 停止一个容器
停止容器的命令如下：

	docker stop <容器 ID>

停止的容器可以通过 docker restart 重启：

	docker restart <容器 ID>

# 进入容器

在使用 -d 参数时，容器启动后会进入后台。此时想要进入容器，可以通过以下指令进入：

	docker attach
	docker exec：推荐大家使用 docker exec 命令，因为此退出容器终端，不会导致容器的停止。

## attach 命令

	docker attach [container ID]

注意： 如果从这个容器退出，会导致容器的停止。

## exec 命令

	docker exec -it [container ID] /bin/bash

注意： 如果从这个容器退出，不会导致容器的停止，这就是为什么推荐大家使用 docker exec 的原因。

# 导出和导入容器

## 导出容器

如果要导出本地某个容器，可以使用 docker export 命令。

	docker export 1e560fca3906 > ubuntu.tar

这样将导出容器快照到本地文件。

## 导入容器快照

可以使用 docker import 从容器快照文件中再导入为镜像，以下实例将快照文件 ubuntu.tar 导入到镜像 test/ubuntu:v1:

	cat docker/ubuntu.tar | docker import - test/ubuntu:v1

# 删除容器

删除容器使用 docker rm 命令：

	docker rm -f [container ID]

