---
title: docker | 在环境下运行自己的代码
date: 2019-12-01 09:33:00
categories:
- docker
tags:
- docker
---
如何在环境下运行自己的代码，这篇博文告诉你，全是我自己总结的。

<!-- more -->

本操作是在 ubuntu 18.04 的环境下操作的。

当我们把 image 下下来之后，我们就可以进入到 docker 的环境中来运行自己的代码了。

我们要理解，对于 docker 来说，它是一个独立的环境。如果，我们在本机写了代码，在 docker 环境中是看不到了，所以我们要挂载本机文件到环境中。

你可以看我的博文来理解什么是挂载。

[ubuntu | 挂载问题](https://benpaodewoniu.github.io/2019/12/01/ubuntu3/)

# docker

假设，我们在 /home/li/code/p.py 有一个 p.py 的文件，我们在本机进入路径

	/home/li/code

那么如果我们不想进入环境，选择直接运行，那么就是下面这条命令

	docker run -v $PWD:/home/li/m -w /home/li/m python:3.5 python p.py

我们来讲一下这条命令

		-v 就是挂载的意思
			$PWD 就是当前目录 -v $PWD:/home/li/m 的意思就是将本机的 /home/li/code 挂载到 docker 环境下的 /home/li/m 目录下
				m 目录 docker 环境会直接创建
		-w 是docker 环境下的路径
			也就是说我们如果进入docker 环境的话，进入的是 /home/li/m 这个目录下
		python:3.5 就是 image
		python 是docker 环境下的python 编译器

当然这是一次性运行，如果，我们想进入环境，在环境里运行，那么我们可以使用下面的命令

	docker run -it -v $PWD:/home/li/m python:3.5 /bin/bash
		上面这条命令是进入这个环境的 bash shell 中，我们进入的是路径是 /
	cd /home/li/m
	python p.py

这样就可以运行了

如果，我们退出了环境的话，再想执行之前的可以参考我下面的博文

[docker | 容器的使用](https://benpaodewoniu.github.io/2019/12/01/docker7/)

# nvidia-docker