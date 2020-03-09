---
title: devpi | 用法
date: 2020-02-21 13:40:16
categories:
- devpi
tags:
- devpi
- python
---
这是我所用的 devpi 的用法。

<!-- more -->

关于如何找到 devpi 的教程，请看下面博文的技巧篇。

[devpi | 本地安装](https://benpaodewoniu.github.io/2020/02/10/devpi0/)

<br/>

# 可以创建多个 index

<br/>

创建多个 index 的原因是，可以在 index 放不同版本的包，比如有的包还在测试不稳定，有的包是稳定版本。

创建index 使用

	devpi index -c latest bases=ant/dev
		(假设我们登陆的是 ant ，那么这个新建的 index 就是 ant/latest ,并且继承了 ant/dev)

查看现在使用的那个目录

	devpi use

{% img /images/devpi/2_0.png %}

换 index ，可以使用下面的命令

	devpi use ant/latest
		有个前提条件就是得先登录到 ant 用户

我们选择不同的index，就可以把相应的包传到对应的 index 里面。

查看所有的 index

	devpi index -l

<br/>

# 包管理

<br/>

我们上传的包怎么看到呢？

登陆对应的用户和index后，使用

	devpi list

{% img /images/devpi/2_1.png %}


然后查看具体的包

	devpi list ant

![](/images/devpi/2_2.png)

可以看到这里面有两个版本。

但是，我们访问网页只能看到一个版本。

![](/images/devpi/2_3.png)

这是因为网页上只显示最新的版本。

删除包

	devpi remove

我们查看一下这个命令怎么使用

	devpi remove -h

在最下面出现

	examples:

	devpi remove pytest

	devpi remove pytest>=2.3.5

	devpi remove https://mydevpi.org/dev/+f/1cf/3d6eaa6cbc5fa/pytest-1.0.zip

我们在上面可以看到 

	devpi list ant

中，一个包的地址是 http://localhost:3141/ant/dev/+f/a8c/47f60ee6e7f32/ant-0.0.2.tar.gz

我们删除就可以这样

	devpi remove http://localhost:3141/ant/dev/+f/a8c/47f60ee6e7f32/ant-0.0.2.tar.gz

那么如何安装指定的包版本呢，对于上面我们既有 0.0.1 还有 0.0.2

我们可以使用下面的命令来安装制定版本

	pip install -i http://localhost:3141/ant/dev ant==0.0.1

这样就可以安装 0.0.1 版本了，祝好！！！

