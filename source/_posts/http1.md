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

# 技术路线

## 客户端

<pre class="mermaid">graph LR
A[Bob<br>输入明文P] -->|P|B["Bob的私钥PRbob<br>加密算法(如RSA)<br>C=E(PRbob,P)"];
B -->|传输数字签名C|C["Alice的公钥环{PUbob,……}<br>解密算法(如RSA)<br>P=D(PUbob,C)"];
C -->|P|D["Alice<br>输出明文P"];</pre>
