---
title: ant | 合约的数量计算
date: 2020-04-17 13:59:18
categories:
- [ant]
- [项目经历,大型,ant]
tags:
- ant
password: antant7
abstract: ant 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
mathjax: true
---
合约一般分为三种，而 ant 项目，会把最后的计价单位变成一种单位。

- BTC-USD
- BTC-USDT

上面一个结算是 USD 一个结算是 BTC ，所以，需要统一最后的结算单位。在 ant 中，统一规定，最后的结算单位是 BTC。

<!-- more -->

先看一下结算函数。

```python
def get_volume(volume, price, contract):
    new_volume = Decimal(volume) * Decimal(contract.contract_val)
    if contract.contract_val_currency.upper() == 'USD' or contract.contract_val_currency.upper() == 'USDT':
        new_volume = new_volume / Decimal(price)
    return float(new_volume)
```

里面很重要的一点是 contract 的 contract_val_currency 属性，现在贴一下 contract 的数据结构。

|属性名称|具体类型|描述信息|
|---|---|---|
|symbol| str|交易对名称 BTCUSD|
|exchange| Exchange| 交易所名字 Exchange.BINANCE|
|name| str|数据结构，通常和 symbol 进行相互补充或者和 symbol 一样|
|product| Product|该数据结构类型 PRODUCT.SPOT|
|websocket_types| list|需要的 websocket 的类型 [WebsocketType.SWAP_MARKET.value]|
|price_precision| int = 4|价格精度|
|amount_precision| int = 0|数量精度|
|minimum_price_increment| float = 0.5|最小的价格增量|
|min_volume| float = 1  |最小交易量|
|min_volume_currency| str = 'btc'|使用哪种货币进行交易|
|maker_fee_rate| float = 0.0|挂单交易费率|
|taker_fee_rate| float = 0.0|吃单交易费率|
|<b style="color: red">contract_val</b>| float = 0.0|合约面值 一张合约代表多少计价货币|
|<b style="color: red">contract_val_currency</b>| str = "BTC"|合约面值计价币种|
|stop_supported| bool = False|是否支持停止下单|
|net_position| bool = False|# whether gateway uses net position volume 这个不懂|
|history_data| bool = False|是否提供历史数据|
|option_strike| float = 0|
|option_underlying| str = "" |vt_symbol 的基础合约|
|option_type| OptionType = None|
|option_expiry| datetime = None|
|funding_rate| float = 0|当期资金费率|
|funding_interval| float = 0|
|funding_next_apply| float = 0|
|funding_start_point| int = 0|
|base_currency| str = ''|币种 如 BTC-USD 的 BTC |
|quote_currency| str = ''|计价货币，如BTC-USD-SWAP中的USD|

可以看出 

- contract_val_currency 代表的是计价货币，具体来说是一张合约代表的货币。
- contract_val，一张合约代表多少的计价货币

下面我们以 gate 为例，来讲述三种合约的数量计算方式。

关于合约的三种分类具体有什么区别，你可以看我下面的博文，非常重要。

[币圈 | 合约到底是什么](https://benpaodewoniu.github.io/2020/04/10/virtualcoin14/)

## 正向合约

以 gate 为例。 

- BTC/USDT 永续合约
	- 其在 gate 上的分类为 USDT 结算合约

其官方文档上这样记载。

|合约|类型|基础货币|计价货币|每张合约的大小|
|---|---|---|---|---|
|BTC/USDT|正向|BTC|USDT|0.0001 BTC|

所以，我们的 contract 数据应该如下，只选取关键的点

|属性名称|具体类型|描述信息|
|---|---|---|
|<b style="color: red">contract_val</b>| 0.0001|合约面值 一张合约代表多少计价货币|
|<b style="color: red">contract_val_currency</b>| BTC|合约面值计价币种|

这样当某一个成交记录是 

- price 7000
- volume 20

的时候，经过

	get_volume(volume,price,contract)

的计算，最后的 volume 是

	0.002

含义是，这次交易的数量是 0.002 BTC。

## 反向合约

- BTC/USD
	- 其在 gate 中的分类是 BTC 结算合约

其文档上这样记录着

|合约|类型|基础货币|计价货币|每张合约的大小|
|---|---|---|---|---|
|BTC/USD|反向|BTC|USD|1 USD|

所以，我们的 contract 数据应该如下，只选取关键的点

|属性名称|具体类型|描述信息|
|---|---|---|
|<b style="color: red">contract_val</b>| 1|合约面值 一张合约代表多少计价货币|
|<b style="color: red">contract_val_currency</b>| USD|合约面值计价币种|

这样当某一个成交记录是 

- price 7000
- volume 700

的时候，经过

	get_volume(volume,price,contract)

的计算，最后的 volume 是

	0.1

含义是，这次交易的数量是 0.1 BTC。

其具体的计算过程是

$$  0.1 = \frac{700 * 1}{7000} $$

## 双币种合约

- ETH/USD
	- 其在 gate 中的分类是 BTC 结算合约

其文档上这样记录着

|合约|类型|基础货币|计价货币|每张合约的大小|
|---|---|---|---|---|
|ETH/USD|双币种|ETH|USD|0.000001 BTC / 1 USD|

所以，我们的 contract 数据应该如下，只选取关键的点

|属性名称|具体类型|描述信息|
|---|---|---|
|<b style="color: red">contract_val</b>| 0.000001 / 1|合约面值 一张合约代表多少计价货币|
|<b style="color: red">contract_val_currency</b>| USD|合约面值计价币种|

这样当某一个成交记录是 

- price 7000
- volume 70000

的时候，经过

	get_volume(volume,price,contract)

的计算，最后的 volume 是

	0.07

含义是，这次交易的数量是 0.07 BTC。

其具体的计算过程是

$$  0.07 = \frac{700 * 1}{0.000001} $$
