---
title: mitmproxy基础
date: 2019-07-01 22:08:05
categories:
- spider
tags:
- spider
- 爬虫
- 手机爬虫
- mitmproxy
---
mitmproxy基础。

<!--more-->

# 和fildder的区别

这个可以嵌入 python 脚本。

# 作用
	
	和正常的代理一样转发请求，保障服务端和客户端的通信
	拦截请求，修改请求，拦截返回，修改返回
	可以载入自定义 python 脚本

mitmproxy 起到的作用是中间人攻击，但是，大前提是攻击的软件，必须主动信任。

# 三个组件

mitmproxy 一共有三个组件

	mitmproxy
	mitmdump
	mitmweb
	
其中，mitmproxy 在windows 不能使用。

# 安装

## 安装环境

	基于 python
	windows操作系统需要安装 Micsoft Visual C++ 14.0 以上
	
这个直接打开 python 命令行，输入

	pip install mitmproxy
	

但是，我安装的时候失败了。

所以，我直接到官网下载了 install 文件。

[官网](https://mitmproxy.org/downloads/#4.0.4/)

![](/images/spider/5_0.png)

安装完成后，打开cmd ，输入 mitmdump --version

如果安装成功，则会弹出版本号。（mitmproxy在win下不能用）

# 配置

我使用的是雷电模拟器。

就好像使用 fildder一样，我们也需要安装证书才可以使用。

首先在cmd中运行

	mitmdump.exe
		当然我们也可以指定端口号 mitmdump.exe -p ***
		

在雷电模拟器中，更改其代理方式，这个更改的方法，我们在fildder中详细介绍过了。

[抓包bilibili APP](https://benpaodewoniu.github.io/2019/06/20/spider2/)

设置完WLAN代理后，我们再用浏览器访问以下网址。
	
	http://mitm.it


然后选择相关平台安装对应的证书就好了。

当我们访问网站的时候，就会看见命令行窗口中一直出现相关的信息。

但是，有时候，命令行窗口并不太适合查看，所以，我们也会开启web的界面进行查看。

![](/images/spider/5_1.png)

选择完那个后，我们按照他们的信息配置代理就好了。

![](/images/spider/5_2.png)

然后访问所给出的网址，在网址上我们可以看到抓包出来的数据。

![](/images/spider/5_3.png)

但是，我个人更喜欢用fildder分析数据。

这样，一个可以抓包的mitdump就配置好了。























