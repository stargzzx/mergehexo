---
title: http | 自己实现一个 http 服务器 NO:5
date: 2020-03-21 16:11:59
categories:
- http
- 自己实现一个服务器
tags:
- http
---
之前，我们定义了简单的 GET 方法，现在，我们定义一下更为复杂的 GET 和 POST 方法。

<!-- more -->

<br/>

# 本章路线图

<br/>


```mermaid
graph TB
	subgraph 自定义
    SimpleHTTPServer---SimpleHTTPRequestHandler
    Client
    end
```