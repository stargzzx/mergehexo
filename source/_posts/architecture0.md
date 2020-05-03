---
title: 编程之道 | 支付宝的架构到底有多牛逼！还没看完我就跪了！「转载」
date: 2020-04-29 12:01:30
categories:
- 架构
- 综合
tags:
- 架构
---

自 2008 年双 11 以来，在每年双 11 超大规模流量的冲击上，蚂蚁金服都会不断突破现有技术的极限。2010 年双 11 的支付峰值为 2 万笔/分钟，到 2017 年双 11 时这个数字变为了 25.6 万笔/秒。

2019 年双 11 的支付峰值比2018 年双 11 支付峰值为 多了十几 万笔/秒，创下新纪录，是 2009 年第一次双 11 的 1360 倍。

在如此之大的支付 TPS 背后除了削峰等锦上添花的应用级优化，最解渴最实质的招数当数基于分库分表的单元化了，蚂蚁技术称之为 LDC（逻辑数据中心）。

本文不打算讨论具体到代码级的分析，而是尝试用最简单的描述来说明其中最大快人心的原理。

<!-- more -->

<br/>

# 参考资料

<br/>

[支付宝的架构到底有多牛逼！还没看完我就跪了！](https://mp.weixin.qq.com/s/b4xKm6IxsP8lcoSxE75dHg)

我想关心分布式系统设计的人都曾被下面这些问题所困扰过：

- 支付宝海量支付背后最解渴的设计是啥？换句话说，实现支付宝高 TPS 的最关键的设计是啥？

- LDC 是啥？LDC 怎么实现异地多活和异地灾备的？

- CAP 魔咒到底是啥？P 到底怎么理解？

- 什么是脑裂？跟 CAP 又是啥关系？

- 什么是 PAXOS，它解决了啥问题？

- PAXOS 和 CAP 啥关系？PAXOS 可以逃脱 CAP 魔咒么？

- Oceanbase 能逃脱 CAP 魔咒么？


如果你对这些感兴趣，不妨看一场赤裸裸的论述，拒绝使用晦涩难懂的词汇，直面最本质的逻辑。

<br/>

# LDC 和单元化

<br/>

LDC（logic data center）是相对于传统的（Internet Data Center-IDC）提出的，逻辑数据中心所表达的中心思想是无论物理结构如何的分布，整个数据中心在逻辑上是协同和统一的。

这句话暗含的是强大的体系设计，分布式系统的挑战就在于整体协同工作（可用性，分区容忍性）和统一（一致性）。

单元化是大型互联网系统的必然选择趋势，举个最最通俗的例子来说明单元化。

我们总是说 TPS 很难提升，确实任何一家互联网公司（比如淘宝、携程、新浪）它的交易 TPS 顶多以十万计量（平均水平），很难往上串了。

因为数据库存储层瓶颈的存在再多水平扩展的服务器都无法绕开，而从整个互联网的视角看，全世界电商的交易 TPS 可以轻松上亿。

这个例子带给我们一些思考：为啥几家互联网公司的 TPS 之和可以那么大，服务的用户数规模也极为吓人，而单个互联网公司的 TPS 却很难提升？

究其本质，每家互联网公司都是一个独立的大型单元，他们各自服务自己的用户互不干扰。

这就是单元化的基本特性，任何一家互联网公司，其想要成倍的扩大自己系统的服务能力，都必然会走向单元化之路。

它的本质是分治，我们把广大的用户分为若干部分，同时把系统复制多份，每一份都独立部署，每一份系统都服务特定的一群用户。

以淘宝举例，这样之后，就会有很多个淘宝系统分别为不同的用户服务，每个淘宝系统都做到十万 TPS 的话，N 个这样的系统就可以轻松做到 N\*十万的 TPS 了。

LDC 实现的关键就在于单元化系统架构设计，所以在蚂蚁内部，LDC 和单元化是不分家的，这也是很多同学比较困扰的地方，看似没啥关系，实则是单元化体系设计成就了 LDC。

小结：分库分表解决的最大痛点是数据库单点瓶颈，这个瓶颈的产生是由现代二进制数据存储体系决定的（即 I/O 速度）。

单元化只是分库分表后系统部署的一种方式，这种部署模式在灾备方面也发挥了极大的优势。

<br/>

# 系统架构演化史

<br/>

几乎任何规模的互联网公司，都有自己的系统架构迭代和更新，大致的演化路径都大同小异。

最早一般为了业务快速上线，所有功能都会放到一个应用里，系统架构如下图所示：

<div style="width: 70%;padding-left: 15%">

![](/images/architecture/0_0.png)

</div>

这样的架构显然是有问题的，单机有着明显的单点效应，单机的容量和性能都是很局限的，而使用中小型机会带来大量的浪费。

随着业务发展，这个矛盾逐渐转变为主要矛盾，因此工程师们采用了以下架构：

<div style="width: 70%;padding-left: 15%">

![](/images/architecture/0_1.png)

</div>

这是整个公司第一次触碰到分布式，也就是对某个应用进行了水平扩容，它将多个微机的计算能力团结了起来，可以完胜同等价格的中小型机器。

慢慢的，大家发现，应用服务器 CPU 都很正常了，但是还是有很多慢请求，究其原因，是因为单点数据库带来了性能瓶颈。

于是程序员们决定使用主从结构的数据库集群，如下图所示：

<div style="width: 70%;padding-left: 15%">

![](/images/architecture/0_2.png)

</div>

其中大部分读操作可以直接访问从库，从而减轻主库的压力。然而这种方式还是无法解决写瓶颈，写依旧需要主库来处理，当业务量量级再次增高时，写已经变成刻不容缓的待处理瓶

这时候，分库分表方案出现了：

<div style="width: 70%;padding-left: 15%">

![](/images/architecture/0_3.png)

</div>

分库分表不仅可以对相同的库进行拆分，还可以对相同的表进行拆分，对表进行拆分的方式叫做水平拆分。

不同功能的表放到不同的库里，一般对应的是垂直拆分（按照业务功能进行拆分），此时一般还对应了微服务化。

这种方法做到极致基本能支撑 TPS 在万级甚至更高的访问量了。然而随着相同应用扩展的越多，每个数据库的链接数也巨量增长，这让数据库本身的资源成为了瓶颈。

这个问题产生的本质是全量数据无差别的分享了所有的应用资源，比如 A 用户的请求在负载均衡的分配下可能分配到任意一个应用服务器上，因而所有应用全部都要链接 A 用户所在的分库，数据库连接数就变成笛卡尔乘积了。

在本质点说，这种模式的资源隔离性还不够彻底。要解决这个问题，就需要把识别用户分库的逻辑往上层移动，从数据库层移动到路由网关层。

这样一来，从应用服务器 a 进来的来自 A 客户的所有请求必然落库到 DB-A，因此 a 也不用链接其他的数据库实例了，这样一个单元化的雏形就诞生了。

思考一下，应用间其实也存在交互（比如 A 转账给 B），也就意味着，应用不需要链接其他的数据库了，但是还需要链接其他应用。

如果是常见的 RPC 框架如 Dubbo 等，使用的是 TCP/IP 协议，那么等同于把之前与数据库建立的链接，换成与其他应用之间的链接了。

为啥这样就消除瓶颈了呢？首先由于合理的设计，应用间的数据交互并不巨量，其次应用间的交互可以共享 TCP 链接，比如 A->B 之间的 Socket 链接可以被 A 中的多个线程复用。

而一般的数据库如 MySQL 则不行，所以 MySQL 才需要数据库链接池。

<div style="width: 70%;padding-left: 15%">

![](/images/architecture/0_4.png)

</div>

如上图所示，但我们把整套系统打包为单元化时，每一类的数据从进单元开始就注定在这个单元被消化，由于这种彻底的隔离性，整个单元可以轻松的部署到任意机房而依然能保证逻辑上的统一。

下图为一个三地五机房的部署方式：

<div style="width: 70%;padding-left: 15%">

![](/images/architecture/0_5.png)

</div>

<br/>

# 蚂蚁单元化架构实践

<br/>

蚂蚁支付宝应该是国内最大的支付工具，其在双 11 等活动日当日的支付 TPS 可达几十万级，未来这个数字可能会更大，这决定了蚂蚁单元化架构从容量要求上看必然从单机房走向多机房。

另一方面，异地灾备也决定了这些 IDC 机房必须是异地部署的。整体上支付宝也采用了三地五中心（IDC 机房）来保障系统的可用性。

跟上文中描述的有所不同的是，支付宝将单元分成了三类（也称 CRG 架构）：

- RZone（Region Zone）：直译可能有点反而不好理解。实际上就是所有可以分库分表的业务系统整体部署的最小单元。每个 RZone 连上数据库就可以撑起一片天空，把业务跑的溜溜的。

- GZone（Global Zone）：全局单元，意味着全局只有一份。部署了不可拆分的数据和服务，比如系统配置等。实际情况下，GZone 异地也会部署，不过仅是用于灾备，同一时刻，只有一地 GZone 进行全局服务。GZone 一般被 RZone 依赖，提供的大部分是读取服务。

- CZone（City Zone）：顾名思义，这是以城市为单位部署的单元。同样部署了不可拆分的数据和服务，比如用户账号服务，客户信息服务等。理论上 CZone 会被 RZone 以比访问 GZone 高很多的频率进行访问。CZone 是基于特定的 GZone 场景进行优化的一种单元，它把 GZone 中有些有着”写读时间差现象”的数据和服务进行了的单独部署，这样 RZone 只需要访问本地的 CZone 即可，而不是访问异地的 GZone。

剩下的还没有看完，等理解了，继续写剩下的。你也可以直接看参考资料。