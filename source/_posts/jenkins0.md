---
title: jenkins | 简介和安装
date: 2020-03-03 11:00:14
categories:
- jenkins
- 基础
tags:
- jenkins
---
老板叫我最近学习 jenkins ，看来我的地位是越来越重要了。

额，也正好符合我的需求，毕竟，技多不压身。

<!-- more -->

<br/>

# 背景

<br/>

使用的是 macbook

<br/>

# 简介

<br/>

<br/>

# 安装

<br/>

jenkins 要使用 brew 进行安装，如果使用 dmg 进行安装的话，后期会导致权限问题。

使用

	brew install jenkins

但是，这个安装方式很慢，所以，建议你参考一下我的博文，使用迅雷辅助，brew 本地安装。

[MacBook | brew 本地安装](https://benpaodewoniu.github.io/2020/02/10/macbook8/)

安装完之后

链接 launchd 配置文件

	ln -sfv /usr/local/opt/jenkins/*.plist ~/Library/LaunchAgents

如果要其他机器也可以访问，把ip地址改为广播地址:

	<string>--httpListenAddress=0.0.0.0</string>

修改完成,执行命令行:

	launchctl load ~/Library/LaunchAgents/homebrew.mxcl.jenkins.plist

命令行启动Jenkins

	jenkins

打开浏览器,输入

	localhost:8080

就可看到Jenkins的web界面

安装插件

![](/images/jenkins/0_0.png)

这一步很慢。

我在没有修改镜像之前，安装插件是失败的，关于如何安装插件请参考我下一篇博文。

## 其他

关闭服务

	launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.jenkins.plist

查看初始密码

	cat /Users/chenpeisong/.jenkins/secrets/initialAdminPassword

其他操作

	http://localhost:8080/exit       //退出Jenkins

	http://localhost:8080/restart  //重启

	http://localhost:8080/reload  //重新加载
