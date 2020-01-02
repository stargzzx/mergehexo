---
title: macbook上的命令行
date: 2019-10-27 15:22:03
categories:
- MacBook
tags:
- MacBook
- 命令行
---
这个是讲我在工作中常用到的命令行操作。
<!-- more -->
MacBook的内核是unix，所以，在某种程度上和 linux 很相近，但是也有所不同，这个介绍一下，我在项目中常用的一些命令。

# 查看端口并且杀死

查看端口占用情况命令
	
	sudo lsof -i :9000

冒号后面就是你需要查看的端口号。

有一个表头名为PID的一列，这一列就表示占用当前端口的进程。

杀掉占用当前端口号的进程

	sudo kill -9 716

-9后面加一个空格，然后加上占用端口的进程PID，就可以杀掉占用端口的进程。最后重启就ok。

# 服务器与本地进行文件互传

下载服务器上的文件夹

	scp -r root@xxx.xx.xxx.xx:/root/htmltitle /Users/xxxxx/Desktop
	
下载服务器上的文件

	scp root@139.59.250.52:/root/htmltitle/htmltitle.cpp /Users/xxxxx/Desktop
	
从本地传到服务器只需要反过来写即可

	scp /Users/xxxxx/Desktop/htmltitle.cpp root@139.59.250.52:/root/htmltitle
