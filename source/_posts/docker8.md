---
title: docker | image
date: 2019-11-30 11:20:40
categories:
- docker
- 基础
tags:
- docker
---
这个讲一下 docker 的 image.

<!--more-->

在看这个 docker 之前，我希望你能看看我的博文，关于镜像的配置。

[docker | 配置镜像](https://benpaodewoniu.github.io/2019/11/30/docker2/)

当运行容器时，使用的镜像如果在本地中不存在，docker 就会自动从 docker 镜像仓库中下载，默认是从 Docker Hub 公共镜像源下载。

下面我们来学习：

<br/>

# 列出镜像列表

<br/>

我们可以使用 docker images 来列出本地主机上的镜像。

![](/images/docker/8_0.png)

各个选项说明:

	REPOSITORY：表示镜像的仓库源
	TAG：镜像的标签
	IMAGE ID：镜像ID
	CREATED：镜像创建时间
	SIZE：镜像大小

同一仓库源可以有多个 TAG，代表这个仓库源的不同个版本，如 ubuntu 仓库源里，有 15.10、14.04 等多个不同的版本，我们使用 REPOSITORY:TAG 来定义不同的镜像。

关于如何使用镜像，你可以看看我下面的博文。

[docker | 容器的使用](https://benpaodewoniu.github.io/2019/12/01/docker7/)

<br/>

# 获取一个新的镜像

<br/>

当我们在本地主机上使用一个不存在的镜像时 Docker 就会自动下载这个镜像。如果我们想预先下载这个镜像，我们可以使用 

	docker pull

命令来下载它。

<br/>

# 查找镜像

<br/>

我们可以从 Docker Hub 网站来搜索镜像，Docker Hub 网址为： https://hub.docker.com/

我们也可以使用 docker search 命令来搜索镜像。比如我们需要一个 httpd 的镜像来作为我们的 web 服务。我们可以通过 docker search 命令搜索 httpd 来寻找适合我们的镜像。

	docker search httpd

![](/images/docker/8_1.jpg)

	NAME: 镜像仓库源的名称
	DESCRIPTION: 镜像的描述
	OFFICIAL: 是否 docker 官方发布
	stars: 类似 Github 里面的 star，表示点赞、喜欢的意思。
	AUTOMATED: 自动构建。

<br/>

# 删除镜像

<br/>

镜像删除使用 docker rmi 命令，比如我们删除 hello-world 镜像：

	docker rmi hello-world

<br/>

# 仓库

<br/>

仓库（Repository）是集中存放镜像的地方。以下介绍一下

[Docker Hub](https://hub.docker.com/)

当然不止 docker hub只是远程的服务商不一样，操作都是一样的。

## Docker Hub

目前 Docker 官方维护了一个公共仓库 

[dDocker Hub](https://hub.docker.com/)

大部分需求都可以通过在 Docker Hub 中直接下载镜像来实现。

## 注册

在 https://hub.docker.com 免费注册一个 Docker 账号。

## 登录和退出

登录需要输入用户名和密码，登录成功后，我们就可以从 docker hub 上拉取自己账号下的全部镜像。

	docker login

## 退出

退出 docker hub 可以使用以下命令：

	docker logout

## 拉取镜像

你可以通过 docker search 命令来查找官方仓库中的镜像，并利用 docker pull 命令来将它下载到本地。

以 ubuntu 为关键词进行搜索：

	docker search ubuntu

![](/images/docker/8_2.jpg)

使用 docker pull 将官方 ubuntu 镜像下载到本地：

	docker pull ubuntu

## 推送镜像

用户登录后，可以通过 docker push 命令将自己的镜像推送到 Docker Hub。

以下命令中的 username 请替换为你的 Docker 账号用户名。

![](/images/docker/8_3.jpg)
