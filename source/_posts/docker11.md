---
title: docker | 远程通过 ssh 连接 docker 里面的 python
date: 2019-12-08 19:52:08
categories:
- docker
tags:
- docker
---
这个非常关键，因为我们可以远程使用docker 的 python 编译器，来调试代码，对于，使用集群的我们来说，非常重要。

<!--more-->

# 参考链接

[PyCharm+Docker：打造最舒适的深度学习炼丹炉](https://zhuanlan.zhihu.com/p/52827335)

首先你需要满足以下两个必备条件：

	使用PyCharm专业版，记住一定是专业版（社区版不提供远程服务）
	在服务器上安装docker和nvidia-docker

根据官网上的 python 的镜像，我拉了一个下来。

[python](https://hub.docker.com/_/python)

{% img /images/docker/11_0.png %}

使用命令：

	docker pull python

然后运行 dcoker

	docker run -it -p 5000:22 python:latest /bin/bash

这句话的意思是让宿主机的 5000 端口和 docker 的22端口连接，以后访问宿主机就和访问dcoker 的22 端口是一样的。

# 配置 ssh 服务

我的镜像中是没有 ssh 服务的，所以，要自己配置

你可以自己配置一下docker 的镜像源，参考我之前的博客。

[ubuntu 更换镜像源](https://benpaodewoniu.github.io/2019/01/19/ubuntu1/)

然后安装 openssh-server

	apt update
	apt install -y openssh-server

然后建立一个配置文件夹并进行必要的配置：

	mkdir /var/run/sshd
	echo 'root:passwd' | chpasswd
	# 这里使用你自己想设置的用户名和密码，但是一定要记住！
	sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
	sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
	echo "export VISIBLE=now" >> /etc/profile

重启SSH激活配置：

	service ssh restart

但是，我上面的配置貌似有错误，因为我不知道密码怎么设置，所以，后面我又重新设置了一边密码

	passwd

这样就可以重新输入密码了

在服务器（宿主机）上（不是服务器的docker里）测试刚刚新建docker容器中哪个端口转发到了服务器的22端口：

	docker port [your_container_name] 22
	# 如果前面的配置生效了，你会看到如下输出
	0.0.0.0:5000

当然，由于我们安装了新的软件，所以，我们要把这个镜像 commit 以下，关于如何 commit ，请关注我下面的博文

[docker | commit 自己的镜像](https://benpaodewoniu.github.io/2019/12/08/docker9/)

先在本地（宿主机）测试一下能否连接成功

	ssh -p 5000 root@127.0.0.1

我在测试的时候出现错误

	Permission denied, please try again.

我的解决方法是配置/etc/ssh/sshd_config

	将PermitRootLogin 改为yes
	去掉PasswordAuthentication yes前的#

然后重启 ssh 服务

	service ssh restart

查看 ssh 是否已经开启，使用下面的命令：

	ps -ef | grep ssh

如果有 sshd 就证明开启了。

然后我们就可以本地测试一下，也就是远程连接，之前的测试都是在宿主机上弄得，在本地，我们在 cmd 中，输入：

	ssh -p 5000 root@[your_host_ip]

根据提示完成连接。

接下来，我们就开始配置 pycharm，让 python 编译器指向 docker 中的 python。

# 在PyCharm里配置部署环境

我们首先要知道python的路径，可以使用以下命令：、

	whereis python
	
打开

	PyCharmTools > Deployment > Configuration

新建一个SFTP服务器，名字自己取：

下面是我自己的配置

{% img /images/docker/11_1.png %}

然后，点击

	Test SFTP connection

如果成功就点击下一步

最后在Mappings中配置路径，这里的路径是你本地存放代码的路径，与刚刚配置的Root Path相互映射（意思是Mapping里本机的路径映射到远程的Root Path），方便以后在本地和远程docker中进行代码和其他文件同步。

{% img /images/docker/11_2.png %}

# 在PyCharm里配置远程解释器

点击PyCharm的

	File > Setting > Project > Project Interpreter

右边的设置按钮新建一个项目的远程解释器：

{% img /images/docker/11_3.jpg %}

根据提示一次填写相关的配置。

配置完成，我的项目解释器如图：

{% img /images/docker/11_4.png %}

这个是因为我们没有安装 pip 的原因，所以，我们要开始安装 pip

[Docker下的ubuntu 安装python3.6 及pip3](https://www.jianshu.com/p/2a5cd519e583)

