---
title: 无显示器使用树莓派
date: 2018-12-27 22:40:35
categories:
- raspberry
tags:
- 树莓派
- raspberry
---
我们可以用我们自己的电脑连接树莓派。

<!--more-->

## 配置说明

电脑：win10

连接软件：putty

[下载地址](https://www.putty.org/)

硬件：树莓派 zero w

## 连接思路

首先我们将树莓派和电脑连在同一个局域网中，然后我们在电脑上用 putty ，根据 IP 来连接树莓派。

## 树莓派的 wifi 配置

我们都是在命令行中操作的，所以我们要先配置 wifi 连接。

修改配置 wifi 配置文件

	sudo nano /etc/wpa_supplicant/wpa_supplicant.conf
	
在打开的文本编辑器中按照下列格式输入

	network={
	ssid=”将我替换成Wi-Fi名”
	psk=”将我替换成Wi-Fi密码”
	priority=将我替换成数字，数字越大代表优先级越高
	}
	
例如我有两个Wi-Fi，Wi-Fi_A优先级高于Wi-Fi_B，那么如下编写即可

	network={
	ssid=”Wi-Fi_A”
	psk=”12345678”
	priority=5
	}
	network={
	ssid=”Wi-Fi_B”
	psk=”12345678”
	priority=4
	}

按Ctrl＋O之后回车保存，按Ctrl＋X退出nano编辑器。

注：

	即使你是用的是Mac进行的SSH，这里依然是使用Ctrl而不是Command
	priority后面不可以是负数

之后你需要重启树莓派，可以使用下面命令完成重启

	sudo reboot
	
## 树莓派开启 SSH

下面我会列出很多种方法开启 SSH。

### 方法一 建立 SSH 空文件

这也是很多教程给的方法，但是我试了试没怎么有效果。。。但是，还是在这里给出。

传说很久很久以前Raspbian默认是开启SSH的，但在一次更新之后，官方说出于安全考虑，于是将Raspbian的SSH设置成了默认关闭状态。

但要开启Raspbian OS的SSH，其实方法简单到不能再简单了，没有显示器，我们只能放弃NOOBS转而使用烧录系统的方法，那么，在烧录完成后，在Boot的根目录新建一个名为SSH的无扩展名的空文件（文件夹可不行哦）即可。

命令行下进入 boot 文件夹下面，输入如下指令

	sudo touch SSH
	
我们就会如愿创建一个 SSH 文件，但是，让人感到奔溃的是，每次开机 SSH 文件都会不见。。。这可能和树莓派的安全机制有关，但是，毕竟让我烦躁，所以，我采用了第二种方法。

### 方法二 用命令行开启

输入命令

	sudo raspi-config
	
出现界面如下，选择第 5 个（我在网上查找的是另外的界面，所以，可能每一版的操作系统界面都不一样，但是只要找找就能找到）

{% img /images/raspberry/8_0.png %}

进去之后，找到 SSH 选项，然后按照步骤来就可以了

{% img /images/raspberry/8_1.png %}

{% img /images/raspberry/8_2.png %}

{% img /images/raspberry/8_3.png %}

至此树莓派的 SSH 就开启了，下面我们就要用 putty 连接了。

## putty 连接树莓派

### 查阅树莓派的 ip

我们先要确保树莓派的 wifi 已经连接，然后在命令行下输入如下命令

	ifconfig

{% img /images/raspberry/8_4.png %}

我们可以获知树莓派的 IP 是 192.168.1.106

我们打开 putty.exe 输入上面的 IP 地址，默认端口是 22 ，几乎不用改，然后点击确认或者open

从网上找的图片

{% img /images/raspberry/8_5.png %}

首次登录会和您确认连接密钥，请按“是”确认。只有首次登录会出现这个提示。

{% img /images/raspberry/8_6.png %}

登录后会提示输入用户名和密码，输入之后即可登录树莓派的命令行。

（提示：Raspbian默认的用户名密码是pi/raspberry）

{% img /images/raspberry/8_7.png %}

### putty 版本选择

推荐PuTTY中文版的原因

就算您阅读英文毫无压力，我也推荐使用中文版的PuTTY。两点理由：

一、默认字体是更大字号、更容易看清的12px（小四）新宋体。

二、与英文原版不同，汉化版做了更改，默认即采用UTF-8编码进行通信。

这样无需任何设置，即可避免Linux命令行显示中文的乱码（变问号、变方块等）问题。

* 如果您使用英文版PuTTY，碰到命令行的中文乱码，请调整连接选项的：

{% img /images/raspberry/8_8.png %}

### putty 连接超时

解决方法：

1、确定你的树莓派连接到网络，能够在树莓派上通过ifconfig查询到IP。

2、并且要确保你的树莓派和你的PC在同一个网段中，也就是IP的前6位要一致。PCIP为192.168.1.100，而小PI的IP为192.168.137.161。

pc 的 ip 可以通过输入如下

	ipconfig
	
{% img /images/raspberry/8_9.png %}

3、确保putty中输入的IP是正确的，并且端号为默认22 。

### 一个关于SSH的小技巧

可以多开。

在SSH连接的状态下，当进行更新下载之类的操作时，可以新开一个终端用SSH接入，这样的话，可以直接完成后面的操作，或者同时下载多个文件都是可以的。












