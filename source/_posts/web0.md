---
title: web 前端 | 路径设置 「ERR_CONNECTION_REFUSED」
date: 2020-05-11 21:38:56
categories:
- 前端设计
- 基础
tags:
- webdesign
- webapp
---
今天部署完之前的一个项目，突然发现资源全部都不能访问了。

使用开发者查看。

所有的资源文件都是这个错误

	net::ERR_CONNECTION_REFUSED

<!-- more -->

然后我查看了资源文件，如下图

![](/images/web/0_0.png)

首先，猜测是不是没有该资源。

上服务器上查看，发现是有该资源的。

所以，继续猜测是路径问题，举一个例子说明

	//localhost/vue/main/***.js

改成

	/vue/main/***.js

最后成功解决问题。