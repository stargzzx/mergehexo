---
title: 币圈 | 标记价格 和 指数价格
date: 2020-04-10 18:21:16
categories:
- [区块链,虚拟币,交易所]
tags:
- 区块链
- virtualcoin
---
做币圈的量化项目很久了，但是，一直都是得过且过，没有真正搞明白币圈某些专业名词的具体含义。

这里总结一下，期货中指数价格和标价价格的含义。

这是来自雪球的帖子。

<!-- more -->

<br/>

# 参考资料

<br/>

[Bit-Z小课堂|防止“恶意爆仓”的“标记价格”是什么？](https://xueqiu.com/1814775139/130863546)

<br/>

# 正文

<br/>

“标记价格”，也称为“公平价格”。

合约作为高杠杆高风险产品，交易所需要有完善的风控体系，来最大可能地保护用户的利益不受侵犯。“标记价格”是有效的风控制度之一

## 合约交易中，我们会接触到三类价格

分别是

- 最新成交价格
- 指数价格 index price
- 标记价格 mark price

## index price

“指数价格”是合约标的_现货在多家现货交易所的相同权重价格之和。

举个栗子：假设三家交易所的BTC_USDT价格分别为：

- 9000
- 9004
- 8999

则计算出的指数价格为：(9000+9003+8998)/3=9001。

该指数价格的组成交易所可能会因为交易所不可预见的不稳定因素而改变。

## mark price

以BTC_USDT永续合约为例：

	标记价格 = 指数价格 * (1 + 资金费用基差率)

	资金费用基差率 = 资金费率 * (至下一个缴付资金费用的时间 / 资金费用时间间隔)


这里我们需要知道的是：

	“资金费用时间间隔”为8个小时；

	“至下一个缴付资金费用的时间 ”<8个小时，

	则 ：“(至下一个缴付资金费用的时间 / 资金费用时间间隔)”<1，

Bit-Z对资金费率进行封顶以确保可以使用最高杠杆。

为了做到这一点，BIt-Z加上了两个限制：

### 绝对的资金费率上限为：（起始保证金 - 维持保证金）* 75%。

举个栗子：假设起始保证金为 1%，维持保证金为 0.5%，则：最大的资金率= (1%-0.5%)\*75% = 0.375%。

### 资金费率在“资金间隔”区间，不得变化大于“维持保证金”的 75％。

这样就把永续合约的“标记价格”限制在了“指数价格”的上下一个微小区间内。

永续合约“标记价格”相当于：指数价格 + 随时间递减的资⾦费⽤基差。

## 为什么使用“标记价格”？

Bit-Z使用“标记价格”来计算合约仓位的未实现盈亏，以及进行强制平仓的判断。

使用“标记价格”可以最大程度地避免，有人恶意短暂操纵合约实时行情，从而导致用户不必要的强制平仓。

## “标记价格”的使用

Bit-Z使用“标记价格”来计算合约仓位的未实现盈亏，以及进行强制平仓的判断。

这意味着，合约委托订单执行后，在持仓的“未实现盈亏”中可能会当即看到正的或负的未实现盈亏，发生这种情况的原因是当前“合约最新成交价格”和“标记价格”有区别。

需要注意的是：平仓时，平仓盈亏是以平仓的成交价格来确定的，因此，会出现未平仓时，持仓的未实现盈亏和平仓后的真实盈亏有差别的情况。

## “标记价格”有效风控制度的总结

今天，我们介绍了合约交易中，作为风控制度之一的“标记价格”。

1. “标记价格”可以有效避免人为短暂操纵价格从而导致用户不必要的强制平仓。

2. “标记价格”用于计算“未实现盈亏”；而“真实平仓盈亏”是由“真实平仓价格”来计算的。

