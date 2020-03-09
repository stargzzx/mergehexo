---
title: docker | 配置镜像
date: 2019-11-30 11:18:40
categories:
- docker
tags:
- docker
---
这也是国内必备吧。。。

<!-- more -->

<br/>

# 镜像加速

<br/>

国内从 DockerHub 拉取镜像有时会遇到困难，此时可以配置镜像加速器。Docker 官方和国内很多云服务商都提供了国内加速器服务，例如：

	Docker官方提供的中国镜像库：https://registry.docker-cn.com
	七牛云加速器：https://reg-mirror.qiniu.com
	网易的镜像地址：http://hub-mirror.c.163.com。
	... ...

当配置某一个加速器地址之后，若发现拉取不到镜像，请切换到另一个加速器地址。国内各大云服务商均提供了 Docker 镜像加速服务，建议根据运行 Docker 的云平台选择对应的镜像加速服务。

## ubuntu

### Ubuntu14.04、Debian7Wheezy

对于使用 upstart 的系统而言，编辑 /etc/default/docker 文件，在其中的 DOCKER_OPTS 中配置加速器地址：

	DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com"

重新启动服务:

	sudo service docker restart

### Ubuntu16.04+、Debian8+、CentOS7

对于使用 systemd 的系统，请在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）：

	{"registry-mirrors":["https://registry.docker-cn.com"]}

![](/images/docker/2_0.jpg)

之后重新启动服务：

	sudo systemctl daemon-reload
	sudo systemctl restart docker

## win10

对于使用 Windows 10 的系统，在系统右下角托盘 Docker 图标内右键菜单选择 Settings，打开配置窗口后左侧导航菜单选择 Daemon。在 Registrymirrors 一栏中填写加速器地址 https://registry.docker-cn.com ，之后点击 Apply 保存后 Docker 就会重启并应用配置的镜像地址了。

![](/images/docker/0_3.png)

## MacOS

在任务栏点击 

	Docker for mac 应用图标 -> Perferences... -> Daemon -> Registry mirrors。

在列表中填写加速器地址即可。修改完成之后，点击 Apply & Restart 按钮，Docker 就会重启并应用配置的镜像地址了。

![](/images/docker/0_2.png)

之后我们可以通过 docker info 来查看是否配置成功。

	$ docker info
	...
	Registry Mirrors:
	 http://hub-mirror.c.163.com
	Live Restore Enabled: false