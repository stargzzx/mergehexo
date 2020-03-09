---
title: 树莓派的图形化连接 VNC
date: 2019-01-14 18:34:02
categories:
- raspberry
tags:
- 树莓派
- 图形化连接
- VNC
---
我们虽然可以用 putty 连接树莓派，但是，我们连接的是命令行界面，但是，有时候，我们需要图形化界面。

但是，我们有时候是没有显示器的，所以我们需要进行图形化连接。

SSH 是纯文本连接，VNC 是图形化连接。

<!--more-->

## 参考资料

[VNC远程登录树莓派的图形界面](http://shumeipai.nxez.com/2013/09/04/login-rpi-with-vnc.html?variant=zh-cn)
[树莓派系列教程：安装系统与配置环境，使用PuTTy与VNC图形界面远程登录](https://www.cnblogs.com/mq0036/p/8488598.html)
[树莓派 VNC Viewer 远程桌面配置教程](http://shumeipai.nxez.com/2018/08/31/raspberry-pi-vnc-viewer-configuration-tutorial.html?variant=zh-cn)
[VNC远程连接树莓派报错问题解决-- “由于目标计算机积极拒绝，无法连接”](https://blog.csdn.net/thystar/article/details/52872543)
[VNC Cannot currently show the dekstop](https://www.raspberrypi.org/forums/viewtopic.php?t=216737)

## 前期准备

我们要连接图形化界面，那么我们所安装的系统也得包含图形化界面。

## 过程

在高端的树莓派中，都集成了一键设置 VNC 大大减少了工作量。

在终端输入以下命令进入配置界面。

	sudo raspi-config
	
![](/images/raspberry/16_0.jpg)

依次操作：Interfacing Options -> VNC -> Yes。之后系统会提示你是否要安装 VNC 服务，输入 y 之后回车，等待系统自动下载安装完成，一切顺利的话 VNC 服务就启动了！

![](/images/raspberry/16_1.jpg)

![](/images/raspberry/16_2.jpg)

### 安装 VNC 客户端

下面去 RealVNC 官网下载 RealVNC Viewer，它是 RealVNC 的客户端，跨平台。下载你需要的平台的客户端版本即可。

[下载地址](https://www.realvnc.com/en/connect/download/viewer/windows/)

下载完成后，直接在上面输入要连接的 IP 就行了。

![](/images/raspberry/16_3.png)

## 需要的注意点

### 安装得系统

要带有图形化界面

### VNC 一直显示 VNC Cannot currently show the dekstop

这个原因可能是我们的开机默认是从命令行界面，而不是图形化界面。

所以，我们要改变启动方式。

### Problem: “由于目标计算机积极拒绝，无法连接

这个可能是因为启动的方式不对，虽然，我们已经设置了开机图形化界面，但是，开机图形化界面一共是两个。

一个是需要输入用户和密码。

一个是直接用默认的账号密码登录。

我们应该选择第一个，如果选择了第二个就会出现这个问题。



