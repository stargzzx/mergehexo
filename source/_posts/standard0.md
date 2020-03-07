---
title: 编程规范 | rest 接口篇
date: 2020-03-03 22:20:39
categories:
- standard
tags:
- standard
- rest
mathjax: true
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

关于这个操作，我接受的那个项目并没有很好的规范，我只用了 get 和 post 请求。

<br/>

# 设计规范

<br/>

协议：使用 HTTPs 协议，确保交互数据的传输安全

域名：应该尽量将 API 部署在专用域名之下。

	https://api.example.com

这点，很多交易所确实将 API 部署在专有域名之下，但是，我的那个投票管理项目其放在专有域名之下。

版本控制：将版本号放在 URL 或者 Header 中。

我公司的项目是将其放在 URL 中，比如

	http://{{host}}/eosvoter/v1/add/account

大部分交易所，也是将版本放在URL中。

路径：只能包含名词，不能包含动词

这个我负责的项目也没有很好的遵守

过滤信息： ?limit=10  ?offset=10 ?page=1

Hypermedia API：在返回结果中提供相关的资源的链接，连向其他的 API 方法

验证：确定用户是其声明的身份，比如提供账户的密码

关于这点，我在量化交易的项目中深有体会。交易所往往会提供一个 key 和一个 secret ，然后我们在 url 或者 header 中加上 key ，再把 key + secret + time 进行加密（或者其他加密方式），放在 header 中。

授权：保证用户对请求资源的操作权限。

这个在交易所上主要有，不同的 key 和 secret 有的可以下单，有的只能查询。

<br/>

# HTTP 常见的状态码

<br/>

|状态码|含义|
|---|---|
|200(OK)|  请求成功|
|201(created)|  资源被成功创建|
|202(accepted)|  已经接受请求，但是尚未完成（异步处理）|
|301(moved permanently)|  资源的 URL 被更新|
|303(see other)|  其他（如，负载均衡等）|
|400(bad request)|  坏请求|
|404(not found)|  资源不存在|
|406(not acceptable)|  服务端不支持所需表示|
|409(conflict)|  通用冲突|
|412(precondition failed)|  前置条件失败（如执行条件更新时的冲突）|
|415(unsupported media type)|  接收到的表示不支持|
|500(internal server error)|  通用错误响应|
|503(service unavailable)|  服务当前无法处理请求|

<br/>

# 返回结果设计

<br/>

	{
		"msg":"url not found",
		"code":"1001",
		"request":"get/add/id"
	}

code 可以根据自己的公司设定来，比如前几位代表部门，后面代表什么业务
