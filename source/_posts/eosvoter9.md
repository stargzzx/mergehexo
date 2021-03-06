---
title: eosvoter | 项目简介 v1.1
date: 2020-02-29 13:37:28
categories:
- [项目经历,中型,eosvoter]
tags:
- eosvoter
---
这个博文可能有点晚了，因为这篇博文是想说，这个项目究竟在干什么。

但是，还是做一个总结。

<!-- more -->

<br/>

# V1.0

<br/>

## 简介

众所周知，eos 是由 21 个超级节点进行管理出块。那么，如何成为超级节点呢？eos 用 pos 机制进行选拔，换句话说，哪一个账户的 eos 量可以排到前 21 位，那么那个节点就自动成为超级节点。

基于这项规定，靠自己来购买 eos 来成为超级节点不是不可能，只不过成本太大了。所以，可采取，联合多个 eos 大户，共同投一个节点，使这个节点成为超级节点后，然后，得到的出块奖励根据各投票者所占比例进行分配。

奖励分为出块奖励和投票奖励。出块奖励只有前 21 个超级节点有，如果，你将票投给节点，而那个节点又是超级节点，那么等它出块的时候，你会有投票奖励。上述的这个过程，你可以进入下面的这个链接进行查看。

[eos.io](https://bloks.io/)

除了明面上的这些奖励之外，我们自然还有其他需要核算的利益。比如，投票某一节点成为超级节点后，这个超级节点的出块奖励和投票奖励该怎么给投票者进行利益分配，正是，我们这个管理系统要做的事情。

希望，通过这个项目，可以让 eos 社区变得更加合理、公开、公正。也更加具有人性化。

## 配置

假设一个节点叫 a ，然后它的旗下有 1，2，3 这样的字节点。a 让 下面的字节点给 b 节点投票。当，b 出块或者获得收益之后，会用获得的收益 / 总的投票数，然后乘以 1，2，3 的总票数，然后根据一个系数，就可以算得 b 需要向 a 支付的价钱。

我们爬取数据，就是要爬取节点投票数，节点出块奖励，然后根据自己配置的那些关系，那些子节点属于哪个节点，然后将数据进行归总放在数据库中。

因为，节点下面有哪些子节点只有项目方才知道，所以，我们需要配置相关的信息才行。

<br/>

# V1.1

<br/>

目前，eosvoter 不只是为了 eos 系统服务了，在此基础上我添加了矿池、币种交易等信息。

所以，我将整个项目命名为 `币币生息coin系统`。

矿池还有脸上交易数据是为了更好的监控资金走向，从而把握大的方向，为核心的量化项目提供数据支持。