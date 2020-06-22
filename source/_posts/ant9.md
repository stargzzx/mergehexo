---
title: ant | 交易所对象 gateway
date: 2020-06-22 15:02:10
categories:
- [项目经历,大型,ant]
tags:
- ant
password: antant9
abstract: ant 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
解析 gateway 的对象细节。

<!-- more -->

整个 gateway 对象从模块分析包括三个，从人口分析只有一个。

我将从下面的三个模块分析

- XXXGateway
- XXXRestApi
- XXXWebsocketApi

<br/>

# XXXGateway

<br/>

这是入口，继承于 `BaseGateway`。

`MainEngine` 初始化也是初始化这个对象，当然，该对象的 `__init__` 有这样一句话

	self.rest_api = XXXSpotRestApi(self)
	self.market_ws_api = XXXWebsocketApi(self, self.rest_api)

所以说该对象，在内部已经初始化了另外两个模块。

`XXXGateway` 主要有两个作用

- 暴露接口
- 三模块联动

## 暴露接口

`XXXRestApi` 和 `XXXWebsocketApi` 是不对外提供接口的，所以，我们通常是调用 `XXXGateway` 的接口来调用另外两个模块的接口。

```python
def connect(self, setting: dict):
    """"""
    self.rest_api.connect(setting)
    self.market_ws_api.connect(setting)
```

## 三模块联动

由于有的时候 websocket 要调用 rest 的接口，但是，两个模块之间是没有连接关系的，所以，我们可以通过 `XXXGateway` 来进行联动。

在 XXXGateway 模块

```python
def __init__(self,event_engine,timer_engine):
	...
	self.rest_api = XXXSpotRestApi(self)
	self.market_ws_api = XXXWebsocketApi(self, self.rest_api)
```

在 XXXRestApi 模块

```python
def __init__(self, gateway: BaseGateway):
	...
	self.gateway = gateway
```

在 XXXWebsocketApi 模块

```python
def __init__(self, gateway):
	...
    self.gateway = gateway
```

所以，在另外两个模块中，gateway 已经被传递。

想要在 XXXWebsocketApi 中调用 XXXRestApi 可以进行下面的调用。

```python
def query_index_price()
	self.gateway.query_index_price()
	# 这个需要在 XXXGateway 中暴露这个接口
	或者
	self.gatewat.rest_api.query_index_price()
```

<br/>

# XXXRestApi

<br/>

- [ant | RestApi](benpaodewoniu.github.io/2020/06/22/ant10/)

<br/>

# XXXWebsocketApi

<br/>

- 