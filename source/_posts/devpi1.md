---
title: devpi | 线上安装
date: 2020-02-20 20:05:16
categories:
- [python,中间件,devpi]
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


经过一些过程，下面的官方教程 Quickstart: uploading, testing, pushing releases 是一种测试版，不是正式开启环境，所以，后面导致了一些问题。

但是，我在这个得记录一下。

<br/>

# 官方测试版（也可以叫体验版）

<br/>

<br/>

## 安装

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

## 开启服务

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

![](/images/devpi/1_0.png)

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

这个命令蛮关键的，只有执行过这个命令的人，会在自己的 home 下产生一个目录，然后才有资格使用 devpi ，否则是没有的。

运维小哥在 root 下使用了 devpi-gen-config ，才有资格使用 devpi

然后，后来他更改了自己文件的一个配置，才能让我的账户和root共同使用：

![](/images/devpi/1_4.png)

上面的多用户可以忽略，因为，这个是测试版本，不算数，当时找错教程了。

接着执行

	devpi use http://localhost:3141

然后在这里又报错了

![](/images/devpi/1_1.png)

因为这里面有一句话是：

	privoxy@localhost

最开始认为是代理将 response 给拒绝了，之前有过这种，代理不走 http ，然后询问运维看能不能撤销代理，运维表示，里面有项目不能关闭。

经过一段时间的探讨，他叫我尝试用一下

	sudo devpi use http://localhost:3141

结果，真的可以了。。。原来是权限不够的原因（我不是 root 用户，但是，登录用户被划到管理员组了，后来运维小哥使用 root 还是需要添加 sudo 真是太奇怪了）

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

![](/images/devpi/1_2.png)

小哥和我说

>你的端口设置那一行应该是127.0.0.1,红框的部分如果变成 这个应该变成:::3141就对了，:::=0.0.0.0   ::1:=127.0.0.1 。127.0.0.1的意思就是只能本机使用。

然后，我们想重新打开服务器，所以，使用

	devpi-server --stop

但是，提示没有开启这项服务，但是，使用命令

	devpi-server --status

却能找到 devpi-server 运行在哪个端口，但是，使用 ps aux | grep 'devpi' 的时候并不能查到 devpi-server 这个信息

但是，使用这个命令，却能看到类似于

	python devpi（比这个长，但是，意思差不多），这个也是相应的 pid

所以，我们打算杀死这个进程，结果，杀死之后，它自己又自动重启了。

后来，我们调用 devpi-server --log

查看日志，日志的意思大概是说，我们在开启 devpi-server 的时候，出现端口占用导致出现错我。

所以，我猜测情况是这样的，devpi-server 的运行程序的pid记录在一个文件中（我找到了），导致，devpi-server --status 的时候，直接读取这个文件，然后说有服务运行，但是，真正想要关闭这个服务的时候，由于找不到，所以报错。

所以，现在的任务就是如何关闭 devpi。

经过，很长时间的摸索，我们在上面的教程中看到了这个。

![](/images/devpi/1_3.png)

所以，这次是一种测试体验，所以，最后我进入我的 home 目录，也就是使用

	supervisord -c gen-config/supervisord.conf

这个命令的目录（去其他目录执行没用，我是在 home 下执行的）

	supervisorctl -c gen-config/supervisord.conf shutdown

最后成功关闭。

在这里，我需要特别说明的是，我本地和线上都执行了这个教程，但是，我本地却有 devpi-server，也能自己关上，其最大的不同是我没执行过这个命令

	supervisord -c gen-config/supervisord.conf

当时，认为麻烦。
 
<br/>

# 正式教程

<br/>

[Quickstart: permanent install on server/laptop](https://devpi.net/docs/devpi/devpi/latest/+d/quickstart-server.html#quickstart-server)

然后运维小哥使用下面的命令启动

	devpi-server --host=0.0.0.0 --start

然后，我成功在站外访问了

但是，这个服务器上什么都没有，包括之前测试版上传的包，这是因为，测试版被我们删了之后，所有的数据也都清空了，所以，还是要在正式环境下使用。

我在本地使用

	pip install -i http://主机ip:3141/root/ant ant --trusted-host 主机ip

成功安装，自此大部分完成。

这里有一个小插曲，就是，运维小哥，在测试机上启用 docker 的时候，其安装并不成功，具体错误是指，其没有符合的版本。

最后查到是因为机器上没有安装 protobuf ，这个我就没在追踪，所以，至此，devpi 配置结束。感谢大家观看！！！