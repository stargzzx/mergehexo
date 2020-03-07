---
title: docker | commit 自己的镜像
date: 2019-12-08 08:58:39
categories:
- docker
tags:
- docker
---
我们在是用别人的镜像的时候，会在原来的镜像中再次下载或者整合一些东西，但是，这些软件是原来镜像中没有的，如果，我们继续运行原来的镜像，那么我们的工作就会不见。

这篇博文会告诉你如何保存我们的工作到镜像中。

<!--more-->

我们的方法是通过 commit ，但是，官方不建议通过commit方式来创建，如果不担心镜像会越来越大的话，这种方式是最自由最简单的，通过dockerfile当然是更优的啦。

<br/>

# 拉线上镜像

<br/>

	docker pull tensorflow/tensorflow

<br/>

# 运行bash

<br/>

	docker run -it tensorflow/tensorflow /bin/bash

安装python3+tensorflow-gpu+keras

<br/>

# 安装完环境后退出docker

<br/>

	exit

<br/>

# 查看所有docker容器，找到要commit的容器id

<br/>

	docker ps -a

结果如下：

{% img /images/docker/9_0.png %}

这里我要commit的容器id为 00ff1b764a1b

<br/>

# commit制作镜像

<br/>

docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]

OPTIONS说明：

	-a :提交的镜像作者；
	-c :使用Dockerfile指令来创建镜像；
	-m :提交时的说明文字；
	-p :在commit时，将容器暂停。

最后的命令是：

	docker commit -a "li" -m "py3.6_tf1.8_keras2.2" 00ff1b764a1b tf_keras:v1

<br/>

# 查看制作出来的镜像

<br/>

	docker images

{% img /images/docker/9_1.png %}

关于我们通过 commit 制作的镜像，我们有两个选择方式，一个是将制作出来的镜像提交到 https://hub.docker.com 另一个是镜像导出导入。

<br/>

# 镜像提交到 https://hub.docker.com

<br/>

通过 commit 出来的镜像非常大，如果要 push 上去的会很麻烦，如果网络不好，那几乎不可能成功，但是，在这里我们还是提一下这个过程。

需要先注册一个账号，然后创建一个REPOSITORY，在本地将镜像标记成和线上一样的REPOSITORY名，最后在本地登录账号并push到线上

因为，我注册 docker 后，并没有给我发激活邮件，很被动。

所以，下面的我都是找的网上的图片。

我创建的REPOSITORY：

{% img /images/docker/9_2.png %}

将镜像标记成同名REPOSITORY

{% img /images/docker/9_3.png %}

登录账号

	docker login

push到线上

{% img /images/docker/9_4.png %}

上传就只能慢慢等了

<br/>

# 镜像导出导入

<br/>

>由于我网络堪忧，要都依赖线上镜像的话，镜像拷贝真的要死要死的了，所以必须线下导出导入

有两种方案：save&load、export&import

## save&load 【保存镜像，并不是容器】

	# save
	docker save vell001/tf-keras > tf-keras.tar
	# load
	docker load < tf-keras.tar

## export&import

Export命令用于持久化容器（不是镜像），不会保留镜像的层级信息，所以大小会比save要小】

	docker export 33f6c8359187 > tf-keras-33f6c8359187.tar
	docker import tf-keras-33f6c8359187.tar






