---
title: http | 自己实现一个 http 服务器 NO:2
date: 2020-03-04 23:38:06
categories:
- [网络,http,自己实现一个服务器]
- [项目经历,小型,http服务器]
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

# 图片说明图

<br/>

![](/images/http/1_0.png)

<br/>

# 本节目录

<br/>


- http
    - handler
        - \_\_init\_\_.py
        - base_handler.py
    - server
        - socket_server.py
    - test
        - test.py

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

## 目录结构

```mermaid
graph LR
    目录:socket-->socket_server.py
    目录:handler-->base_handler.py
```

- socket 是处理 TCPServer
- handler 是处理 StreamRequsetHandler

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

```mermaid
graph LR
    目录:socket-->socket_server.py
```

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
                self.process_request(request, client_address)
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

## base_handler.py

```mermaid
graph LR
    目录:handler-->base_handler.py
```


```python
# -*- encoding=utf-8 -*-


class BaseRequestHandler:
    def __init__(self, server, request, client_address):
        self.server = server
        self.request = request
        self.client_address = client_address

    def handle(self):
        pass


class StreamRequestHandler(BaseRequestHandler):

    def __init__(self, server, request, client_address):
        BaseRequestHandler.__init__(self, server, request, client_address)

        self.rfile = self.request.makefile('rb')
        self.wfile = self.request.makefile('wb')
        self.wbuf = []

    # 编码：字符串->字节码
    def encode(self, msg):
        if not isinstance(msg, bytes):
            msg = bytes(msg, encoding='utf-8')
        return msg

    # 解码：字节码->字符串
    def decode(self, msg):
        if isinstance(msg, bytes):
            msg = msg.decode()
        return msg

    # 读消息
    def read(self, length):
        msg = self.rfile.read(length)
        return self.decode(msg)

    # 读取一行消息
    def readline(self, length=65536):
        msg = self.rfile.readline(length).strip()
        return self.decode(msg)

    # 写消息
    def write_content(self, msg):
        msg = self.encode(msg)
        self.wbuf.append(msg)

    # 发送消息
    def send(self):
        for line in self.wbuf:
            self.wfile.write(line)
        self.wfile.flush()
        self.wbuf = []

    def close(self):
        self.wfile.close()
        self.rfile.close()
```

<br/>

# 编写测试用例

<br/>

## test.py

```mermaid
graph LR
    目录:test-->test.py
```

```python
# -*- encoding=utf-8 -*-

import time
import socket
import threading

from server.socket_server import TCPServer
from handler.base_handler import StreamRequestHandler

from server.base_http_server import BaseHTTPServer
from handler.base_http_handler import BaseHTTPRequestHandler


class TestBaseRequestHandler(StreamRequestHandler):

    # 具体处理的逻辑(Echo)
    def handle(self):
        msg = self.readline()
        print('Server recv msg: ' + msg)
        time.sleep(1)
        self.write_content(msg)
        self.send()
        pass


# 测试SocketServer(TCPServer)
class SocketServerTest:
    def run_server(self):
        tcp_server = TCPServer(('127.0.0.1', 8888), TestBaseRequestHandler)
        tcp_server.serve_forever()

    # 客户端的具体连接逻辑
    def client_connect(self):
        client = socket.socket()
        client.connect(('127.0.0.1', 8888))
        client.send(b'Hello TCPServer\r\n')
        msg = client.recv(1024)
        print('Client recv msg: ' + msg.decode())

    # 生成客户端
    def gen_clients(self, num):
        clients = []
        for i in range(num):
            client_thread = threading.Thread(target=self.client_connect)
            clients.append(client_thread)
        return clients

    def run(self):
        server_thread = threading.Thread(target=self.run_server)
        server_thread.start()

        time.sleep(5)

        clients = self.gen_clients(10)
        for client in clients:
            client.start()

        server_thread.join()
        for client in clients:
            client.join()



if __name__ == '__main__':
    SocketServerTest().run()
```

上面就完成了简单的例子，但是，目前完成的服务器还非常初级，甚至，是单线程工作，下一步，我们将代码改编成多线程。


其实只需要修改

- socket_server.py

将里面的处理函数，变成多线程处理，其全部代码如下所示

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

    # 多线程处理请求
    def process_request_multithread(self, request, client_address):
        t = threading.Thread(target=self.process_request,
                             args=(request, client_address))
        t.start()

    # 关闭请求
    def close_request(self, request):
        request.shutdown(socket.SHUT_WR)
        request.close()

    # 关闭服务器
    def shutdown(self):
        self.is_shutdown = True
```
