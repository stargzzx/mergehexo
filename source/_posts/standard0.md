---
title: 编程规范 | rest 接口篇
date: 2020-03-03 22:20:39
categories:
- standard
tags:
- standard
- rest
---
rest 是一种架构风格，你也可以理解成接口规范，是一种不具有广义性标准的规范。

举一个简单的例子来说，电梯的 2 号按键，其作用是上二楼的，但是，这个按键是圆的还是方的，取决于设计公司。

rest 的作用规范是有的，但是，具体的实施取决于写接口的人。

<!-- more -->

我在实习的过程中，由于独立完成了一个项目，那个项目使用的是前后端分离，其通信使用的是 rest 请求。

我在后面重点参与的一个项目，量化交易，目前对接了 40 多个交易所，深刻了解了不同公司对 restful 和 websocket 接口的书写标准的不同。

我将结合具体的案例来回顾 rest 接口规范，另外这个章节是参考下面的教程目录来的。

[REST API 接口测试](https://www.imooc.com/learn/1048)

<br/>
# 生命周期
<br/>

接口测试在单元测试之后，UI测试之前。（这个不懂）

<br/>
# 接口测试
<br/>

接口测试（Application Programming Interface）即 api 测试。

接口测试是测试系统组件间接口的一种测试。重点在于数据转移。

接口测试一般用于多系统间的交互开发，或者拥有多个子系统的应用系统开发测试。

举一个我经手项目的一个例子：eos投票管理系统，其接口就是前端和后端进行数据交互的（前后端分离）。

<br/>
# rest 和 restful 的区别
<br/>

rest = representational state transfer

这两个是同一种东西， restful 只是 rest 的形容词，rest 的目的是 client 和 server 进一步解耦。

其借助的是 http 。

<br/>
# rest 操作
<br/>

rest的核心思想是资源，其有四种操作资源的方式

- 创建资源
	- HTTP POST
- 获取资源
	- HTTP GET
- 删除资源
	- HTTP DELETE
- 更新资源
	- HTTP PUT

rest 还有更多的操作，但是，以上四种就已经满足几乎所有的分类了。

下面是一些不常见的分类。

- 获取某个资源的头部信息
	- HTTP HEAD
- 更新资源的部分属性
	- HTTP PATCH



