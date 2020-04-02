---
title: 股票 | 富途接口对接
date: 2020-04-01 17:24:35
categories:
- [股票,futu]
tags:
- 股票
- 富途
---
话不多少，开始对接股票接口。

之所以选用富途是因为我看 vn.py 中获取美股数据用的是富途。

<!-- more -->

<br/>

# 参考资料

<br/>

[API 文档](https://futunnopen.github.io/futu-api-doc/intro/intro.html)
[官网](https://www.futunn.com/download/openAPI?lang=zh-CN)

<br/>

# 架构分析

<br/>

![](/images/stock/1_0.png)

上面这个图是富途官网提供的。

这张图告诉你，你用 python 脚本向 FutuOpenD 发送指令，然后 FutuOpenD 根据这个指令向 Futu 的服务器，发送请求，服务器把数据返回给 FutuOpenD ，然后 FutuOpenD 再返回给 python 脚本。

那个第一步，我们是是什么？下载 FutuOpenD ？不不不，第一步，我们现在 futu 上开户。

<br/>

# 安装

<br/>

## 开户

我们在富途牛牛上注册一个账号，然后开户，这个开户需要身份证信息等，一系列的东西。审核流程很快，我自己大概 5 分钟就通过了，不过，我是真的不想开户。。。工作需要。

## 下载 FutuOpenD

进入官网

[官网](https://www.futunn.com/download/openAPI?lang=zh-CN)

我们将会看到两种客户端。

![](/images/stock/1_1.png)

图中的 1 代表的是行情客户端，你可以根据这个客户端买卖股票，查询行情，但是，这个不能和 python 脚本产生交互。

![](/images/stock/1_2.png)

图中的 2，3，4 是正确的下载方式，根据自己的系统版本下载相应的软件。

![](/images/stock/1_3.png)

## 安装相应的库

根据

[API 文档](https://futunnopen.github.io/futu-api-doc/intro/intro.html)

我们需要安装他提供的包

	pip install futu-api

然后我们根据他们 API 提供的接口写相应的指令就好了。