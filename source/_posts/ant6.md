---
title: ant | 自定义的数据结构解析
date: 2020-04-10 14:37:04
categories:
- [ant]
- [项目经历,大型,ant]
tags:
- ant
mathjax: true
password: antant6
abstract: ant 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
ant数据收集模块，作为连接交易所和数据展示模块的中间件，起到了将数据结构化、规整化，使得每个交易所返回的数据按照 ant 项目的要求进行返回。

这就要求我们必须有明确的数据结构来规整。

下面我将从两个方面来解析。

- 现货
- 期货

另外需要注明的是，数据收集模块仅仅只是起到搜集行情的作用，对于订单、私人数据是不做安排的，也就是，我们通常只会搜集 public 数据。

<!-- more -->

<br/>

# 现货

<br/>

目前，我们现货需要的数据结构有

- ContractData
- TickData
- TradeData
- DepthData


## ContractData

该数据结构定义的是 symbol 信息，其具体的细节如下。

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
|contract_val| float = 0.0|合约面值 一张合约代表多少计价货币|
|contract_val_currency| str = "BTC"|合约面值计价币种|
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

这个其实是一个综合数据结构，返回的数据结构有的是期货才有的属性，如

- contract_val
- contract_val_currency

上面两个数据非常重要，因为在计算期货的交易量的时候，我们通常将其数量变成 base_currency 的数量，但是，往往作为期货的话，其数量是按照张的方式给出，比如

	BTCUSD_SWAP: 1 张等于 5 USD

所以，我们要根据下面的函数来计算以 base_currency 的数量

```python
def get_volume(volume, price, contract):
    new_volume = Decimal(volume) * Decimal(contract.contract_val)
    if contract.contract_val_currency.upper() == 'USD':
        new_volume = new_volume / Decimal(price)
    return float(new_volume)
```

其计算的过程如下，现在以 BTCUSD_SWAP 为例：

假如，返回数据如下

- volume 567 张
- price 为 BTC 现在的价格，比如 7564 USD
- contract_val 为 50 ，即一张代表多少 USD

所以，最后按照 BTC 的计价数量应该为

$$  volume(BTC) = \frac{volume \times contract.val}{price} $$

## TickData

|属性名称|具体类型|描述信息|
|---|---|---|
|symbol| str|交易对 BTCUSD|
|exchange| Exchange| 交易所名字 Exchange.BINANCE|
|name| str = ""|和 symbol 进行相互补充，一般和 symbol 相同|
|volume24h| float = 0|24 小时的成交量|
|last_price| float = 0|上一次成交的价格|
|last_volume| float = 0|上一次成交的数量|
|high_price| float = 0|24 小时最高价格|
|low_price| float = 0|24 小时最低价格|
|pre_close| float = 0|未知|
|nano_time| int = 0|该数据结构向外传递时候的纳秒级时间|

这里特别注意的是 nano_time ，这个时间是现生成的，是构造完这个数据结构后生成的。

	nano_time=time.time_ns()

这个属性的意义在其他数据结构中也是相同的意义。

## TradeData

|属性名称|具体类型|描述信息|
|---|---|---|
|symbol| str|交易对 BTCUSD|
|exchange| Exchange| 交易所名字 Exchange.BINANCE|
|name| str = ""|和 symbol 进行相互补充，一般和 symbol 相同|
|trade_id| str|交易 ID|
|side| OrderSide|买卖方向 sell buy|
|price| float = 0|交易价格|
|volume| float = 0|交易数量|
|nano_time| int = 0|该数据结构向外传递时候的纳秒级时间|
|direction| Direction = None|期货方向，做空还是做多|
|offset| Offset = None|交易发生的类型比如 NONE = "NONE" OPEN = "OPEN"#开 CLOSE = "CLOSE"#平|
|pending_bid_volume| float = 0| 前pending_depth档买盘挂单价值总合|
|pending_ask_volume| float = 0| 前pending_depth档卖盘挂单价值总合|

需要注意的是

- pending_bid_volume
- pending_ask_volume

这两个数据是通过 DepthData 计算得到的。

也就是，一共有两种类型的 trade ，一个是交易所成交的 trade ，一个是通过 DepthData 计算得到的。

下面说一下，为什么会有计算 trade 的需求。

```python
def calc_pending_volume(depth, expect_depth=20):
    """
    计算买、卖盘的挂单总量（每一档位的价格*挂单量的总合）
    :param expect_depth: 期望统计的深度，默认 20， 部分交易所可能不够，以实际返回的depth为准
    :param depth: depth 对象
    :return:
        pending_bid_volume 买盘挂单总量， 以base currency 为单位
        pending_ask_volume 卖盘挂单总量， 以base currency 为单位
        depth 实际统计的深度
    """
    pending_bid_volume, pending_ask_volume = Decimal(0), Decimal(0)
    if not depth:
        return pending_bid_volume, pending_ask_volume

    for idx in range(1, expect_depth + 1):
        bid_volume = depth.__getattribute__(f'bid_volume_{idx}')
        ask_volume = depth.__getattribute__(f'ask_volume_{idx}')
        pending_bid_volume += Decimal(bid_volume)
        pending_ask_volume += Decimal(ask_volume)
    if depth.exchange == Exchange.FTX_SWAP or depth.exchange == Exchange.FTX_FUTURES or \
            depth.exchange == Exchange.FTX:
        return float(pending_bid_volume) / 50, float(pending_ask_volume) / 50
    if depth.symbol == 'XBT_CM':
        return 0, 0
    return float(pending_bid_volume), float(pending_ask_volume)
```

这个数据一定程度上反映了交易所的深度信息，深度越好，交易所成交比例也就越大，数据的变化也一定程度上反映市场情绪。

同时有一点需要知道，交易所返回的数据往往用 bid 和 ask 来表示买卖双方，其对照如下

- bid -> buy
- ask -> sell

## DepthData

|属性名称|具体类型|描述信息|
|---|---|---|
|symbol| str|交易对 BTCUSD|
|exchange| Exchange| 交易所名字 Exchange.BINANCE|
|name| str = ""|和 symbol 进行相互补充，一般和 symbol 相同|
|nano_time| int = 0|该数据结构向外传递时候的纳秒级时间|
|bid_price_1| float = 0|买单第一档的价格 一共 25 档|
|ask_price_1| float = 0|卖单第一档的价格 一共 25 档|
|bid_volume_1| float = 0|买单第一档的数量 一共 25 档|
|ask_volume_1| float = 0|卖单第一档的数量 一共 25 档|

需要注意的是有的交易所返回的可能不只是 25 档 也可能返回的 少于 25 档。

<br/>

# 期货

<br/>

期货同样有现货的数据结构

- ContractData
- TickData
- TradeData
- DepthData

在这里需要特别注明的是，期货的 volume 的计价方式，一般，目前对接的所有的期货 volume 都要进行转化。

除了上面的数据结构，期货还同样有下面两个

- FutureBaseData
- LiquidationData

## FutureBaseData

|属性名称|具体类型|描述信息|
|---|---|---|
|symbol| str|交易对 BTCUSD|
|exchange| Exchange| 交易所名字 Exchange.BINANCE|
|name| str = ""|和 symbol 进行相互补充，一般和 symbol 相同|
|nano_time| int = 0|该数据结构向外传递时候的纳秒级时间|
|open_interest| float = 0|持仓量，为全局持仓量|
|volume24h| float = 0|24 小时交易量，通常通过 ticker 数据进行获得|
|funding_rate| float = 0|当前资金费率 只有永续合约有|
|estimated_rate| float = 0|预计下 8 小时资金费率 只有永续合约有|
|mark_price| float = 0|标记价格|
|index_price| float = 0|指数价格|

## LiquidationData

|属性名称|具体类型|描述信息|
|---|---|---|
|symbol| str|交易对 BTCUSD|
|exchange| Exchange| 交易所名字 Exchange.BINANCE|
|name| str = ""|和 symbol 进行相互补充，一般和 symbol 相同|
|nano_time| int = 0|该数据结构向外传递时候的纳秒级时间|
|side| OrderSide|交易方向|
|volume| float = 0|交易数量|
|price| float = 0|交易价格|
|loss| float = 0  | 穿仓用户亏损|
|order_create_time| str = ''  | 强平单的委托时间|