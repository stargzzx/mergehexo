---
title: 树莓派与文件系统连接 FileZilla
date: 2018-12-29 13:09:33
categories:
- raspberry
tags:
- 树莓派
- raspberry
- FileZilla
---
虽然 SSH 可以方便的在局域网中链接，但是，如果要传输多个文件，这又很不方便，所以，推荐一个傻瓜式连接。
SFTP。
<!--more-->
SFTP全名叫SSH File Transfer Protocol，从名字就可以看出来，这和SSH有关系。但这不重要，你只需要知道的是它不需要你在树莓派上面配置任何东西或者安装任何服务，只要SSH可以链接，SFTP就可以工作。
首先介绍一款开源SFTP软件——FileZilla。windows和Mac os平台都有，下载安装就不说了。这里放出官网下载链接：
[下载链接](https://www.filezilla.cn/download/client)
安装完成以后，打开FileZilla，在上面分别输入树莓派的ip地址、账户、账户密码、端口
这里的端口填和 SSH 一样的端口，22.我开了 putty 和 SFTP 都可以用 22 端口同时连接。
{% img /images/raspberry/11_0.png %}
然后点击快速连接，右边出现文件就连接成功了。
{% img /images/raspberry/11_1.png %}
如何用呢？很简单，找到想要传送的文件从左边拖到右边就上传给树莓派了。反之则下载到本机。
{% img /images/raspberry/11_2.png %}



