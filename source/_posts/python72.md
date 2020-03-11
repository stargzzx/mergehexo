---
title: python | 网络编程 socket
date: 2020-03-11 18:22:43
categories:
- python
tags:
- python
- socket
- 网络编程
mathjax: true
---
这个是很低层的网络编程，我将会使用这个模块编写一个 http + tcp 的简单网络服务器。

这一篇我将会介绍一下 socket 的用法。

<!-- more -->

<br/>

# Python 网络编程

<br/>

Python 提供了两个级别访问的网络服务。：

- 低级别的网络服务支持基本的 Socket，它提供了标准的 BSD Sockets API，可以访问底层操作系统Socket接口的全部方法。

- 高级别的网络服务模块 SocketServer， 它提供了服务器中心类，可以简化网络服务器的开发。


<br/>

# 什么是 Socket?

<br/>

Socket又称"套接字"，应用程序通常通过"套接字"向网络发出请求或者应答网络请求，使主机间或者一台计算机上的进程间可以通讯。

首先我们知道浏览器通过请求获得来自 web 服务器的资源。在这里，我们将浏览器看作是一个进程， web 服务器也看作是一个进程。那么，浏览器和 web 服务器间的通信，就可以比照于进程间的通信。

那么，进程有很多（并行），那么进程间是如何识别其他进程的呢？

- 使用端口来标记不同的网络进程
	- 端口使用 16 个比特位表示（0～65535）
- 使用 IP 来标记不同的计算机

通过端口和不同 IP 的组合，就可以标记不同计算机的进程了。

而，IP + 多口的组合就是套接字。

套接字（socket） 是一个抽象概念，表示 TCP 连接的一端（比如，客户端浏览器）。

<br/>

# socket()函数

<br/>

Python 中，我们用 socket（）函数来创建套接字，语法格式如下：

	socket.socket([family[, type[, proto]]])

参数

- family
	- 套接字家族可以使AF_UNIX或者AF_INET
- type
	- 套接字类型可以根据是面向连接的还是非连接分为SOCK_STREAM或SOCK_DGRAM
- protocol
	- 一般不填默认为0


| socket 类型|描述|
|---|---|
|socket.AF_UNIX|只能够用于单一的Unix系统进程间通信|
|socket.AF_INET|服务器之间网络通信|
|socket.AF_INET6|IPv6|
|socket.SOCK_STREAM|流式socket , for TCP|
|socket.SOCK_DGRAM|数据报式socket , for UDP|
|socket.SOCK_RAW|原始套接字，普通的套接字无法处理ICMP、IGMP等网络报文，而SOCK_RAW可以；其次，SOCK_RAW也可以处理特殊的IPv4报文；此外，利用原始套接字，可以通过IP_HDRINCL套接字选项由用户构造IP头。|
|socket.SOCK_SEQPACKET|可靠的连续数据包服务|
|创建TCP Socket：|s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)|
|创建UDP Socket：|s=socket.socket(socket.AF_INET,socket.SOCK_DGRAM)|

<br/>

# Socket 对象(内建)方法

<br/>

|函数|描述|
|---|---|
|服务器端套接字|---|
|s.bind()|绑定地址（host,port）到套接字， |在AF_INET下,以元组（host,port）的形式表示地址。
|s.listen()	|开始TCP监听。backlog指定在拒绝连接之前，操作系统可以挂起的最大连接数量。该值至少为1，大部分应用程序设为5就可以了。|
|s.accept()|被动接受TCP客户端连接,(阻塞式)等待连接的到来|
|客户端套接字|
|s.connect()|	主动初始化TCP服务器连接，。一般address的格式为元组（hostname,port），如果连接出错，返回socket.error错误。|
|s.connect_ex()|	connect()函数的扩展版本,出错时返回出错码,而不是抛出异常|
|公共用途的套接字函数||
|s.recv()|接收TCP数据，数据以字符串形式返回，bufsize指定要接收的最大数据量。flag提供有关消息的其他信息，通常可以忽略。|
|s.send()|	发送TCP数据，将string中的数据发送到连接的套接字。返回值是要发送的字节数量，该数量可能小于string的字节大小。|
|s.sendall()|	完整发送TCP数据，完整发送TCP数据。将string中的数据发送到连接的套接字，但在返回之前会尝试发送所有数据。成功返回None，失败则抛出异常。|
|s.recvfrom()|	接收UDP数据，与recv()类似，但返回值是（data,address）。其中data是包含接收数据的字符串，address是发送数据的套接字地址。|
|s.sendto()|	发送UDP数据，将数据发送到套接字，address是形式为（ipaddr，port）的元组，指定远程地址。返回值是发送的字节数。|
|s.close()|	关闭套接字|
|s.getpeername()|	返回连接套接字的远程地址。返回值通常是元组（ipaddr,port）。|
|s.getsockname()|	返回套接字自己的地址。通常是一个元组(ipaddr,port)|
|s.setsockopt(level,optname,value)|	设置给定套接字选项的值。|
|s.getsockopt(level,optname[.buflen])|	返回套接字选项的值。|
|s.settimeout(timeout)|	设置套接字操作的超时期，timeout是一个浮点数，单位是秒。值为None表示没有超时期。一般，超时期应该在刚创建套接字时设置，因为它们可能用于连接的操作（如connect()）|
|s.gettimeout()|	返回当前超时期的值，单位是秒，如果没有设置超时期，则返回None。|
|s.fileno()|	返回套接字的文件描述符。|
|s.setblocking(flag)|	如果flag为0，则将套接字设为非阻塞模式，否则将套接字设为阻塞模式（默认值）。非阻塞模式下，如果调用recv()没有发现任何数据，或send()调用无法立即发送数据，那么将引起socket.error异常。|
|s.makefile()|	创建一个与该套接字相关连的文件|

<br/>

# Python Internet 模块

<br/>

以下列出了 Python 网络编程的一些重要模块：

|协议	|功能用处	|端口号|	Python 模块|
|---|---|---|---|
|HTTP	|网页访问	|80|	httplib, urllib, xmlrpclib|
|NNTP	|阅读和张贴新闻文章，俗称为"帖子"	|119|	nntplib|
|FTP	|文件传输	|20|	ftplib, urllib|
|SMTP	|发送邮件	|25|	smtplib|
|POP3	|接收邮件	|110|	poplib|
|IMAP4	|获取邮件	|143|	imaplib|
|Telnet	|命令行	|23|	telnetlib|
|Gopher	|信息查找	|70|	gopherlib, urllib|

<br/>

# 例子

<br/>

你可以看下面的专栏，来增加对这个库的理解。

[手动写 http 服务](https://benpaodewoniu.github.io/categories/http/)

