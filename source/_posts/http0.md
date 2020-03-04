---
title: http | 自己实现一个 http 服务器 NO:1
date: 2020-03-04 10:53:09
categories:
- http
tags:
- http
mathjax: true
---
如果你关注最近的找工作，就会发现大厂越来越重视基础知识了，并且往往会让你详细介绍 http 原理甚至实现。

本次章节就是跟着网上的教程来一步步实现一个 http 服务器。

[从0到1 实现HTTP服务器开发](https://www.imooc.com/learn/1172)

<!-- more -->

<br/>
# TCP 协议极简入门
<br/>

## TCP/IP 四层模型

网络接口层 -> 网络层 -> 传输层 -> 应用层

网络|作用对象
---|---|---
网络接口层|  物理设备
网络层|  IP协议
传输层|  TCP协议
应用层| HTTP协议

HTTP 服务器是工作在 应用层和传输层的。

## TCP

Transmission Conreol Protocol 传输控制协议

- 解决协议可靠传输
- 流量控制
	- 感知对方压力并控制流量（普通计算机受到网卡的限制，在应对从服务器传来的数据后，不能及时处理，当服务器感知后，会减少发送数据）
- 拥塞控制
	- 感知网络压力并控制发送速度（作用对象时网络，不是client）

## TCP 报文

其有两个部分组成

- TCP首部
- TCP数据报的数据（字节流）
	- 来自应用层报文

我要首先要知道的一点是， TCP 是面向字节流的协议，也就是说，无论应用层的报文是 utf-8 编码，还是图片又或者是音视频，都在 TCP 报文中转化成字节流。

<br/>
# 网络套接字
<br/>

首先我们知道浏览器通过请求获得来自 web 服务器的资源。在这里，我们将浏览器看作是一个进程， web 服务器也看作是一个进程。那么，浏览器和 web 服务器间的通信，就可以比照于进程间的通信。

那么，进程有很多（并行），那么进程间是如何识别其他进程的呢？

- 使用端口来标记不同的网络进程
	- 端口使用 16 个比特位表示（0～65535）
- 使用 IP 来标记不同的计算机

通过端口和不同 IP 的组合，就可以标记不同计算机的进程了。

而，IP + 多口的组合就是套接字。

套接字（socket） 是一个抽象概念，表示 TCP 连接的一端（比如，客户端浏览器）。

## 编程对象

套接字编程可以分为服务端的编程和客户端的编程

### 服务端的编程

1. 创建套接字
2. 绑定（bind）套接字
3. 监听（listen）套接字
4. 处理信息

### 客户端的编程

1. 创建套接字
2. 连接（connect）套接字
3. 处理信息

### 代码展示

#### server.py

{% codeblock %}
# -*- encoding=utf-8 -*-


import socket

def server():
    # 1. 创建套接字
    s = socket.socket()
    # 2. 绑定
    HOST = '127.0.0.1'
    PORT = 6666
    s.bind((HOST, PORT))
    # 3. 监听
    s.listen(5)
    # 4. 处理
    while True:
        c, addr = s.accept()
        print('Connect client: ', addr)
        msg = c.recv(1024)
        print('From client: %s' % msg)
        c.send(msg)
    pass


if __name__ == '__main__':
    server()
{% endcodeblock %}

#### client.py

{% codeblock %}
# -*- encoding=utf-8 -*-


import socket


def client():
    # 1. 创建套接字
    s = socket.socket()
    # 2. 连接
    HOST = '127.0.0.1'
    PORT = 6666
    s.connect((HOST, PORT))
    # 3. 处理信息
    s.send(b'Hello World!')
    msg = s.recv(1024)
    print('From server: %s' % msg)


if __name__ == '__main__':
    client()
{% endcodeblock %}

