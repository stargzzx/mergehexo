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

# ping

<br/>

用来检查网络是否通畅或者网络连接速度的命令 

	ping www.baidu.com

<br/>

# telnet

<br/>

Linux telnet命令用于远端登入。

执行telnet指令开启终端机阶段作业，并登入远端主机。「一般用 ssh 来完成上面的作用」。

但是，通常用来探测指定ip是否开放指定端口

通过

	telnet ip port

可以判断某一 IP 的 端口是否对外开放。

	# telnet 47.75** 8090
	Trying 47.75.**...
	Connected to 47.75.**. 「表示连接成功，该端口已经开启」
	Escape character is '^]'.
	Connection closed by foreign host.

如果没有开启，可能会停留在

	Trying 47.75.***...

## 退出连接

如果连接不成功可以

	control + c 「MacBook」

如果连接成功，可以使用

	quit
	如果quit不能退出，可以先执行
	control + ] 然后 quit 退出
	
<br/>

# ssh

<br/>

关于 `ssh` 你可以点击下面的链接。

- [ssh](https://benpaodewoniu.github.io/categories/ssh/)

## ssh 和 telnet 的区别

`telnet` 和 `ssh` 都是连接远程计算机的连接协议，可以完成对完成计算机的控制，方便维护。

他们都是基于`TCP/IP`协议下的，所以连接时都需要知道目标机的网址或者域名.

他们都是与远程主机连接的通道，完成的目的是一样的，只不过手段不一样而已。

由于 `ssh` 经过加密算法加密，收报文需要解密，发报文需要加密，导致其传输速度、效率较telnet低很多，然而，它却有telnet不具有的安全性「明文链接」。

<br/>

# ifconfig

<br/>

查看和修改网络配置

eth0是指具体的网络设备，如果有多个网络设备，0依次累加

这个可以设置网卡之类的，可以单独的 google 一下。


<br/>

# netstat

<br/>

netstat命令的功能是显示网络连接、路由表和网络接口信息，可以让用户得知目前都有哪些网络连接正在运作。

- `a` 或 `--all` 显示所有连线中的Socket。
- `A` <网络类型> 或 --<网络类型> 列出该网络类型连线中的相关地址。
- `c` 或 `--continuous` 持续列出网络状态。
- `C` 或 `--cache` 显示路由器配置的快取信息。
- `e` 或 `--extend` 显示网络其他相关信息。
- `F` 或 `--fib` 显示FIB。
- `g` 或 `--groups` 显示多重广播功能群组组员名单。
- `h` 或 `--help` 在线帮助。
- `i` 或 `--interfaces` 显示网络界面信息表单。
- `l` 或 `--listening` 显示监控中的服务器的Socket。
- `M` 或 `--masquerade` 显示伪装的网络连线。
- `n` 或 `--numeric` 直接使用IP地址，而不通过域名服务器。
- `N` 或 `--netlink` 或 `--symbolic` 显示网络硬件外围设备的符号连接名称。
- `o` 或 `--timers` 显示计时器。
- `p` 或 `--programs` 显示正在使用Socket的程序识别码和程序名称。
- `r` 或 `--route` 显示Routing Table。
- `s` 或 `--statistice` 显示网络工作信息统计表。
- `t` 或 `--tcp` 显示TCP传输协议的连线状况。
- `u` 或 `--udp` 显示UDP传输协议的连线状况。
- `v` 或 `--verbose` 显示指令执行过程。
- `V` 或 `--version` 显示版本信息。
- `w` 或 `--raw` 显示RAW传输协议的连线状况。
- `x` 或 `--unix` 此参数的效果和指定"-A unix"参数相同。
- `-ip` 或 `--inet` 此参数的效果和指定"-A inet"参数相同。

## netstat -an | grep LISTEN

0.0.0.0的就是每个IP都有的服务，写明哪个IP的就是绑定那个IP的服务。

## netstat -tln

用来查看linux的端口使用情况,我一般用这个。

## netstat -a

查看所有的服务端口（LISTEN，ESTABLISHED）

<br/>

# ufw

<br/>

`Ubuntu` 系统进行安装的时候默认安装了 `ufw防火墙`.

## ufw status

	状态：不活动

不活动意味着没开启，不意味着没安装

	开启防火墙: ufw enable
		开启的过程中，会提示你 ssh 可能中断，选择 yes 即可
		此时再执行 ufw status 就会出现暴漏的端口了
	开启端口: ufw allow 22
		开启完成，需要重启防火墙生效
	重启防火墙: ufw reload

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
	ufw reload

我就是通过这个修好了我外部不能连接的问题。

ps: 如果你是阿里云服务器，你要开启两个防火墙，一个是部署机器，一个是阿里云页面。