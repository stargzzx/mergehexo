---
title: ant | 业务逻辑分析 总篇
date: 2020-06-22 13:36:50
categories:
- [项目经历,大型,ant]
tags:
- ant
password: antant8
abstract: ant 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
在接下来的几篇中，我将从以下角度来将整个 ant 交易所对接系统进行解读。

- 业务逻辑
- 源码层面

<!-- more -->

<br/>

# 总的描述

<br/>

`ant` 是量化交易的其中一个模块，可以称之为交易所对接系统。这个系统乃是整个大系统的开始系统。

该系统的作用是对接交易所的各个接口，包括 `restful` 和 `websocket`。我们所获得得主要数据是

- depth
- tick
- trade

等，因为，每一个交易所有自己的一套标准，所以，这个系统还要统一转化这些差异，将所有的数据进行标准化、兼容性输出。

对于整个大系统而言，该系统为数据展示系统提供可视化的源数据、为策略系统提供波动数据。系统要求稳定、鲁棒性高。

除了对接 public 数据之外，该系统目前还要对接四大所「ok、币安、gate、火币」private 接口，来为策略系统提供下单操作。

该系统其底层的原理是：`事件驱动` ，将获得的数据推到 `kafka` 中，供可视化系统展示。

对于上述所描写的一些名字，你可以阅读我下面的博文。

- [事件驱动](https://benpaodewoniu.github.io/categories/%E6%9E%B6%E6%9E%84/%E4%BA%8B%E4%BB%B6%E9%A9%B1%E5%8A%A8/)
- [编程规范 | rest 接口篇](https://benpaodewoniu.github.io/2020/03/03/standard0/)
- [websocket](https://benpaodewoniu.github.io/categories/%E7%BD%91%E7%BB%9C/websocket/)

该系统主要是对接下面的交易所的接口

- spot
- swap
- futures

当然，还对接了 `options`、`股票`接口。

<br/>

# 流程逻辑

<br/>

![](/images/ant/8_0.png)

从数据库中获得相关信息

- 交易所名字
- 交易对信息等

存储在一个变量中。

接着初始化 `MainEngine` 引擎。「其中根据源码来说，在 `MainEngine` 初始化的过程中，`TimerEngine`、`EventEngine`、`LogEngine`、`OmsEngine` 都已经在 `MainEngine` 内部初始化好了，并且，`EventEngine` 在初始化的时候，就已经开启了接收事件的线程，而`OmsEngine` 也在 `EventEngine` 中注册了一些事件」。

接着，独立引擎 `MonitorEngine` 会检查传进来的交易所是否可以连接成功，只有连接成功的交易所才能进入 `MainEngine` 。

然后，`MainEngine` 会将传进来的交易所，进行初始化，并且，进行交易所的连接。

该连接主要有两种，一个是

- restful 的 contract 查询
	- 检查交易所中是否有数据库所存储的 symbol
	- 对应的 event 为 EVENT_CONTRACT
- websocket 的连接初始化
	- 对应的 event 为 EVENT_SOCKET_CONNECT

上面的两个状态信息都被传入 `EventEngine` 的消息队列中。

接着，`MainEngine` 初始化 `KafkaEngine` ，并且，`KafkaEngine` 进行了 start 这个函数，是将整个系统都启动起来「restful 和 websocket 都连接成功」，包括向 `EventEngine` 注册事件、开始 websocket 订阅。

至此，一个完整的过程都已经说明。

下面我将会具体来说明每一个引擎、接口、封装的对象等。

其具体的主体如下

- MainEngine
- EventEngine
- KafkaEngine
- gateway
- restful
- websocket

<br/>

# gateway

<br/>

- [ant | 交易所对象 gateway](https://benpaodewoniu.github.io/2020/06/22/ant9/)

<br/>

# restful

<br/>

- [ant | RestApi](https://benpaodewoniu.github.io/2020/06/22/ant10/)