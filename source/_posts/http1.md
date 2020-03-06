---
title: http | 自己实现一个 http 服务器 NO:2
date: 2020-03-04 23:38:06
categories:
- http
tags:
- http
mathjax: true
---
这一章节我们将实现 TCP 协议的套接字服务的编程，为后续应用层 HTTP 协议 做准备。

<!-- more -->

<br/>
# 技术路线
<br/>

画图是真的烂，我也是第一次用这个插件，见谅，2333～～～

```mermaid
graph TB
	subgraph 自定义
    SimpleHTTPServer---SimpleHTTPRequestHandler
    SimpleHTTPServer---Client
    end
    subgraph HTTP
    BaseHTTPSercer---BaseHTTPRequsetHandler
    BaseHTTPSercer ==> SimpleHTTPServer
    BaseHTTPRequsetHandler ==> SimpleHTTPRequestHandler
    end
    subgraph TCP
    TCPServer---StreamRequsetHandler
    TCPServer ==> BaseHTTPSercer
    StreamRequsetHandler ==> BaseHTTPRequsetHandler
    end
```

在这里说明一下

- TCP 端
	- TCPServer ：接收客户端的 TCP 连接
	- StreamRequsetHandler ：封装字节流网络请求处理功能
- HTTP端
	- BaseHTTPSercer ：基础 HTTP 服务器
	- BaseHTTPRequsetHandler ：封装 HTTP 请求处理的基础功能
- 自定义
	- SimpleHTTPRequestHandler ：实现自定义的 HTTP 请求（GET、POST）处理逻辑

<br/>
# 本章完成的内容
<br/>

## 技术路线图

```mermaid
graph TB
    subgraph TCP
    TCPServer---StreamRequsetHandler
    end
```

- TCP 端
	- TCPServer ：接收客户端的 TCP 连接
	- StreamRequsetHandler ：封装字节流网络请求处理功能

## 涉及的技术

```mermaid
graph LR
    TCPServer-->StreamRequsetHandler
    StreamRequsetHandler-->多线程TCPServer
```

- TCPServer
	- 需要 TCP 服务器的工作原理
- StreamRequsetHandler
	- 需要 TCP 连接处理的原理
- 多线程TCPServer
	- 需要服务端多线程处理请求模型

<br/>
# TCPServer
<br/>

后面那些都是方法

```mermaid
graph LR
    TCPServer
    TCPServer-->启动
    启动-->serve_forever
    TCPServer-->处理请求
    处理请求-->get_request
    处理请求-->process_request
    处理请求-->close_request
    TCPServer-->正常关闭
    正常关闭-->shutdown
    TCPServer-->a(处理对象:服务端地址-处理器-套接字)
```

## socket_server.py

```python
# -*- encoding=utf-8 -*-

import socket
import threading

class TCPServer:

    def __init__(self, server_address, handler_class):
        self.server_address = server_address
        self.HandlerClass = handler_class
        self.socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.is_shutdown = False

    # 服务器的启动函数
    def serve_forever(self):
        self.socket.bind(self.server_address)
        self.socket.listen(10)
        # while True:
        while not self.is_shutdown:
            # 1. 接收请求
            request, client_address = self.get_request()
            # 2. 处理请求
            try:
                # self.process_request(request, client_address)
                self.process_request_multithread(request, client_address)
            except Exception as e:
                print(e)

    # 接收请求
    def get_request(self):
        return self.socket.accept()

    # 处理请求
    def process_request(self, request, client_address):
        handler = self.HandlerClass(self, request, client_address)
        handler.handle()
        # 3. 关闭连接
        self.close_request(request)


    # 关闭请求
    def close_request(self, request):
        request.shutdown(socket.SHUT_WR)
        request.close()

    # 关闭服务器
    def shutdown(self):
        self.is_shutdown = True
```

<br/>
# StreamRequsetHandler
<br/>

- BaseRequestHandler
	- 定义连接处理的基本操作 & 相关属性
- StreamRequsetHandler
	- 主要作用是 封装 TCP 处理逻辑


```mermaid
graph LR
	BaseRequestHandler
	BaseRequestHandler==>StreamRequsetHandler
    StreamRequsetHandler
    StreamRequsetHandler-->编码/解码
    编码/解码-->encode/decode
    StreamRequsetHandler-->读/写消息
    读/写消息-->read/readline
    读/写消息-->write_content
    读/写消息-->send
```






