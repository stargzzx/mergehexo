---
title: 如何在手机上查询设计的 webapp
date: 2018-07-26 15:54:33
categories:
- 网站设计
- 前端设计
- 基础
tags:
- webdesign
- webapp
---
当我们在电脑端写 webapp 代码之后，会有两个途径检查结果。

<!-- more -->

## 电脑浏览器

以 google 浏览器为例，按下 F12 进入开发者模式，我们可以切换到手机模式，并且，我们有很多种手机型号的选择。

## 用自己的手机查看

我觉得最佳方式就是用自己的手机查看。

先介绍一下背景。

### 工程背景

集成环境： wamp (apache + mysql + php)

关于 wamp 你可以看我下面的文章。

[wamp安装和启动](https://benpaodewoniu.github.io/2018/07/25/wamp0/)

前提条件是电脑端和手机端连接同一个局域网。

当我们在电脑端搭建好，并且有如下链接访问成功：

	http://localhost:8080/test/

这个时候我们要获取我们电脑的 IP 地址，然后通过这个 IP 地址来访问电脑文件。

### IP 地址的获取

打开 cmd ，输入 ipconfig ，找到 ipv4 地址。

比如 我的 ipv4 地址是 192.168.1.100

所以在手机端我将访问的地址是：
	
	http://192.168.1.100:8080/test/