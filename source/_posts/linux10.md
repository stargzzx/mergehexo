---
title: linux | 网络相关
date: 2020-04-23 13:53:45
categories:
- [linux,基础]
tags:
- linux
---
这是网络相关的技术贴。

<!-- more -->

<br/>

# netstat

<br/>

netstat命令的功能是显示网络连接、路由表和网络接口信息，可以让用户得知目前都有哪些网络连接正在运作。

- a或--all 显示所有连线中的Socket。
- A<网络类型>或--<网络类型> 列出该网络类型连线中的相关地址。
- c或--continuous 持续列出网络状态。
- C或--cache 显示路由器配置的快取信息。
- e或--extend 显示网络其他相关信息。
- F或--fib 显示FIB。
- g或--groups 显示多重广播功能群组组员名单。
- h或--help 在线帮助。
- i或--interfaces 显示网络界面信息表单。
- l或--listening 显示监控中的服务器的Socket。
- M或--masquerade 显示伪装的网络连线。
- n或--numeric 直接使用IP地址，而不通过域名服务器。
- N或--netlink或--symbolic 显示网络硬件外围设备的符号连接名称。
- o或--timers 显示计时器。
- p或--programs 显示正在使用Socket的程序识别码和程序名称。
- r或--route 显示Routing Table。
- s或--statistice 显示网络工作信息统计表。
- t或--tcp 显示TCP传输协议的连线状况。
- u或--udp 显示UDP传输协议的连线状况。
- v或--verbose 显示指令执行过程。
- V或--version 显示版本信息。
- w或--raw 显示RAW传输协议的连线状况。
- x或--unix 此参数的效果和指定"-A unix"参数相同。
- -ip或--inet 此参数的效果和指定"-A inet"参数相同。

## netstat -an | grep LISTEN

0.0.0.0的就是每个IP都有的服务，写明哪个IP的就是绑定那个IP的服务。

## netstat -tln

用来查看linux的端口使用情况,我一般用这个。

## netstat -a

查看所有的服务端口（LISTEN，ESTABLISHED）

<br/>

# telnet

<br/>

Linux telnet命令用于远端登入。

执行telnet指令开启终端机阶段作业，并登入远端主机。

## 退出连接

如果连接不成功可以

	control + c 「MacBook」

如果连接成功，可以使用

	quit
	如果quit不能退出，可以先执行
	control + ] 然后 quit 退出

<br/>

# 外部访问

<br/>

我们开启了一个网络服务端口，怎么才能让外部的浏览器访问它呢？

首先本地监听的地址必须是 `0.0.0.0` 而不能是 `localhost` 或者 `127.0.0.1`.

	tcp        0      0 127.0.0.1:8091          0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:18183           0.0.0.0:*               LISTEN     
	tcp        0      0 127.0.0.1:5000          0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:12138           0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:6379            0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:83              0.0.0.0:*               LISTEN     
	tcp        0      0 0.0.0.0:84              0.0.0.0:*               LISTEN     
	tcp6       0      0 :::8086                 :::*                    LISTEN     
	tcp6       0      0 :::8088                 :::*                    LISTEN     
	tcp6       0      0 :::2375                 :::*                    LISTEN     
	tcp6       0      0 :::12138                :::*                    LISTEN     
	tcp6       0      0 :::3306                 :::*                    LISTEN     
	tcp6       0      0 :::80                   :::*                    LISTEN

在上面这个例子里， `0.0.0.0` 和 `:::` 都是可以被外部访问的，也就是端口 `18183` 是可以被外部访问的。

但有时候，我们开启了某一个端口的 `0.0.0.0` 的监听，但是，还是不能被外部访问，这个时候可能是由于我们开启了防火墙。

## 防火墙

查看端口开启状态

	ufw status

开启某个端口，比如我开启的是 `8381`

	ufw allow 8381

开启防火墙

	ufw enable

关闭防火墙

	ufw disable

重启防火墙

	ufw reload

禁止外部某个端口比如 `80`

	ufw delete allow 80

假设，我们查询 `0.0.0.0:5010` 不能被外部访问，可以使用下面的命令试一试

	ufw allow 5010

我就是通过这个修好了我外部不能连接的问题。

ps: 如果你是阿里云服务器，你要开启两个防火墙，一个是部署机器，一个是阿里云页面。