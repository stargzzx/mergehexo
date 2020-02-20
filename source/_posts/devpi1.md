---
title: devpi | 线上安装
date: 2020-02-20 20:05:16
categories:
- devpi
tags:
- devpi
- python
---
先说一下背景，运维给我在测试机上新建了一个账户，给了那个账户 root 权限。

测试机（线上机）是 ubuntu。

再读这个之前，你可以看看关于 devpi 的本地安装，那边博文涉及到了一些技巧。

[devpi | 本地安装](https://benpaodewoniu.github.io/2020/02/10/devpi0/)

<!-- more -->

<br/>
# 背景
<br/>

我们的测试机用的是 python3.7 ，所以，其 pip 也是 pip3.7。测试机自带翻墙，不过很慢。

<br/>
# 安装
<br/>

我是严格按照官方的教程来的。其链接如下：

 [Quickstart: uploading, testing, pushing releases](https://devpi.net/docs/devpi/devpi/5.2/+d/quickstart-releaseprocess.html#installing-devpi-client-and-server)

 在这里要特别说明，在网上有很多流传的 devpi 的教程，但是，大多数都已经过期了，所以，你应该先看看官网的介绍。

 我安装的 devpi 的版本是 5.1.1。

 登录到线上机之后，执行

 	pip3.7 install -U devpi-web devpi-client

好不容易安装好了，但是最后一步提醒，没有权限。并且，安装速度太慢了，经过，运维小哥提醒后，执行了

	sudo pip3.7 install -i https://pypi.tuna.tsinghua.edu.cn/simple  -U devpi-web devpi-client

运转如飞。

安装好之后，打算开启服务。

<br/>
# 开启服务
<br/>

执行

	devpi-init

结果出错，

	ModuleNotFoundError: No module named '_sqlite3'

经过查阅相关的资料后

[Linux ModuleNotFoundError: No module named '_sqlite3'](https://blog.csdn.net/qq_38687287/article/details/84840615)

## 第一步

安装缺失的sqlite3库

	sudo apt-get install libsqlite3-dev

安装完成后，可以试试

	whereis sqlite3

## 第二步

重新编译 python，这里我和运维小哥探讨了一下，如果重新编译 python 的话，会不会导致环境崩溃，小哥说，只要你不删除那些库，就不会，最后我重新编译了环境和各种依赖库都没变化。

因为，我要重新编译，所以，我特地查了我想要重新编译的python版本是 3.7.2 所以，我只要安装这个版本就好了。

[各种 python 官方版本](https://www.python.org/ftp/python/)

找到相应的版本，然后找到下载的 url

{% img /images/devpi/1_0.png %}

执行（下面的 url 是我胡编的，所以，去官方找）

	wget https://www.python.org/ftp/python/3.7.2/Python-3.7.2.tar.xz（这一步特别慢。。。，在这里吐槽一下）

	tar -xvf Python-3.7.2.tar.xz && cd Python-3.7.2

	./configure --enable-loadable-sqlite-extensions

	make && sudo make install

然后就顺利重新编译了 python3.7.2。然后，我们就执行

	devpi-init

没有报错

继续执行

	devpi-gen-config

接着执行

	devpi use http://localhost:3141

然后在这里又报错了

{% img /images/devpi/1_1.png %}

因为这里面有一句话是：

	privoxy@localhost

最开始认为是代理将 response 给拒绝了，之前有过这种，代理不走 http ，然后询问运维看能不能撤销代理，运维表示，里面有项目不能关闭。

经过一段时间的探讨，他叫我尝试用一下

	sudo devpi use http://localhost:3141

结果，真的可以了。。。原来是权限不够的原因（我不是 root 用户，但是，登录用户被划到管理员组了）

创造账户

	sudo devpi user -c ant password=123

登陆

	sudo devpi login ant --password=123

选择这个账户，继承谁，这里继承的是，最基本的 pypi，并且设置 index （目录组）

	sudo devpi index -c dev bases=root/pypi

使用这个目录组

	sudo devpi use ant/dev

每一步 devpi 都会有相应的信息打出来，你可以到官方网站上看一下，对照自己有没有做错。

在这里，我们看到提示有

	current devpi index: http://localhost:3141/ant/dev (logged in as ant)

就想着本地能不能访问一下线上

打开浏览器，输入

	http://ip地址:3141/ant/dev

结果并不能。

让运维小哥查看一下发现：

{% img /images/devpi/1_2.png %}

小哥和我说

>你的端口设置那一行应该是127.0.0.1,红框的部分如果变成 这个应该变成:::3141就对了，:::=0.0.0.0   ::1:=127.0.0.1 。127.0.0.1的意思就是只能本机使用。

后面，我的主要任务就是解决如何让线下链接线上，但是，问题，暂时还没解决。未完待续。。。




