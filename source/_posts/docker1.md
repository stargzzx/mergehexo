---
title: docker | 基本操作
date: 2019-11-30 10:01:14
categories:
- docker
tags:
- docker
---
这里是 docker 的基本操作，祝你快速入门！

<!-- more -->

<br/>

# docker 版本

<br/>

Docker 是一个开源的商业产品，有两个版本：

	社区版（Community Edition，缩写为 CE）
	企业版（Enterprise Edition，缩写为 EE）

企业版包含了一些收费服务，个人开发者一般用不到。下面的介绍都针对社区版。

<br/>

# docker 安装

<br/>

## ubuntu

这里特别说明，其它 linux 版本有其他的安装方法，请参考：

[docker安装](https://www.runoob.com/docker/ubuntu-docker-install.html)

当然，你也可以直接去官网看相关的教程，再次不在细说。

### 卸载旧版本

Ubuntu 系统默认自带的 docker.io 安装包安装Docker，但版本可能相对较旧.

Docker 的旧版本被称为 docker，docker.io 或 docker-engine 。如果已安装，请卸载它们：

	sudo apt-get remove docker docker-engine docker.io containerd runc

当前称为 Docker Engine-Community 软件包 docker-ce 。

### Ubuntu 18.04 安装 Docker-ce

当然还有其它 ubuntu 版本的可以点击上面的链接。

更换国内软件源，推荐中国科技大学的源，稳定速度快（可选）

你可以看我在博客上之前写的关于更换ubuntu源的文章。
[ubuntu 更换镜像源](https://benpaodewoniu.github.io/2019/01/19/ubuntu1/)
[ubuntu 镜像进阶篇](https://benpaodewoniu.github.io/2019/11/30/ubuntu2/)

	sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
	sudo sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list
	sudo apt update

安装需要的包

	sudo apt install apt-transport-https ca-certificates software-properties-common curl

添加 GPG 密钥，并添加 Docker-ce 软件源，这里还是以中国科技大学的 Docker-ce 源为例，这里要

	curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
	sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu (lsb_release -cs) stable"

添加成功后更新软件包缓存

	sudo apt update

安装 Docker-ce

	sudo apt-get install docker-ce docker-ce-cli containerd.io

设置开机自启动并启动 Docker-ce（安装成功后默认已设置并启动，可忽略）

	sudo systemctl enable docker
	sudo systemctl start docker

测试运行

	sudo docker run hello-world

{% img /images/docker/0_4.png %}


## MacOS

[Install Docker Desktop on Mac](https://docs.docker.com/docker-for-mac/install/)

macOS 我们可以使用 Homebrew 来安装 Docker。

Homebrew 的 Cask 已经支持 Docker for Mac，因此可以很方便的使用 Homebrew Cask 来进行安装：

	brew cask install docker

在载入 Docker app 后，点击 Next，可能会询问你的 macOS 登陆密码，你输入即可。之后会弹出一个 Docker 运行的提示窗口，状态栏上也有有个小鲸鱼的图标.

你也可以直接去官网上下载。

点击顶部状态栏中的鲸鱼图标会弹出操作菜单。

{% img /images/docker/0_0.png %}

{% img /images/docker/0_1.png %}

<br/>

# 安装验证

<br/>

	docker version
	# 或者
	docker info

<br/>

# 添加当前用户到 docker 用户组

<br/>

docker 默认使用 root 权限，如果不想使用 sudo 或者切换 root 的话，可以将当前的用户添加到 docker 用户组中

添加当前用户到 docker 用户组，可以不用 sudo 运行 docker

	sudo groupadd docker
	sudo usermod -aG docker $USER
		上面这句话是在 root 权限下执行的，其中 $USER 替换成用户名

测试添加用户组

	docker run hello-world

<br/>

# 镜像

<br/>

关于镜像，你可以参考我的博客

[docker | 配置镜像](https://benpaodewoniu.github.io/2019/11/30/docker2/)

<br/>

# image 文件

<br/>

Docker 把应用程序及其依赖，打包在 image 文件里面。只有通过这个文件，才能生成 Docker 容器。image 文件可以看作是容器的模板。Docker 根据 image 文件生成容器的实例。同一个 image 文件，可以生成多个同时运行的容器实例。

image 是二进制文件。实际开发中，一个 image 文件往往通过继承另一个 image 文件，加上一些个性化设置而生成。举例来说，你可以在 Ubuntu 的 image 基础上，往里面加入 Apache 服务器，形成你的 image。

	列出本机的所有 image 文件。
	docker image ls

	删除 image 文件
	docker image rm [imageName]

image 文件是通用的，一台机器的 image 文件拷贝到另一台机器，照样可以使用。一般来说，为了节省时间，我们应该尽量使用别人制作好的 image 文件，而不是自己制作。即使要定制，也应该基于别人的 image 文件进行加工，而不是从零开始制作。

为了方便共享，image 文件制作完成后，可以上传到网上的仓库。Docker 的官方仓库 

[Docker Hub](https://hub.docker.com/)

是最重要、最常用的 image 仓库。此外，出售自己制作的 image 文件也是可以的。

<br/>

# 实例：hello world

<br/>

我们在上面运行过，但是，我们这次说一下它的运行原理。

首先，运行下面的命令，将 image 文件从仓库抓取到本地。

	docker image pull library/hello-world

上面代码中，docker image pull是抓取 image 文件的命令。

library/hello-world是 image 文件在仓库里面的位置。

其中library是 image 文件所在的组，hello-world是 image 文件的名字。

由于 Docker 官方提供的 image 文件，都放在

[library](https://hub.docker.com/u/library)

组里面，所以它的是默认组，可以省略。因此，上面的命令可以写成下面这样。

	docker image pull hello-world

抓取成功以后，就可以在本机看到这个 image 文件了。

	docker image ls

现在，运行这个 image 文件。

	docker container run hello-world

docker container run命令会从 image 文件，生成一个正在运行的容器实例。

注意，docker container run命令具有自动抓取 image 文件的功能。如果发现本地没有指定的 image 文件，就会从仓库自动抓取。

因此，前面的docker image pull命令并不是必需的步骤。

如果运行成功，你会在屏幕上读到下面的输出。

	Hello from Docker!
	This message shows that your installation appears to be working correctly.
	... ...

输出这段提示以后，hello world就会停止运行，容器自动终止。

有些容器不会自动终止，因为提供的是服务。比如，安装运行 Ubuntu 的 image，就可以在命令行体验 Ubuntu 系统。

	docker container run -it ubuntu bash

对于那些不会自动终止的容器，必须使用docker container kill 命令手动终止。

	docker container kill [containID]



