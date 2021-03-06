---
title: 量化投资 | 慕课 python量化投资
date: 2020-03-31 15:08:19
categories:
- [量化投资,基础,慕课 python 量化投资]
tags:
- 量化投资
mathjax: true
---
该教程参考慕课网的量化投资，是免费教程。

[Python量化投资](https://www.imooc.com/learn/1206)

<!-- more -->

关于量化投资你需要知道三个理论基础。

- 市场有效性假说
- 现代资产定价理论
- 投资组合理论

<br/>

# 市场有效性假说

<br/>

## 参考资料

[市场有效性假说是过时的书呆子理论么？](https://xueqiu.com/3926587841/71244912)
[如何理解有效市场假说？](https://www.zhihu.com/question/54777214)

市场有效性假说(Efficient Market Hypothesis，以下简称EMH)由芝加哥大学的金融教授Eugene Fama在1960年代提出。在EMH中，Fama教授提出了三种有效程度不同的市场。

- 弱式有效市场假说 (Weak Form)
- 半强式有效市场假说(Semi—Strong Form)
- 强式有效市场假说(Strong Form)

## 强式有效市场假说(Strong Form)

在最有效的市场中，证券的价格反映了所有公开和不公开的信息。信息全部体现在价格里面了，无论内幕与否，所以没办法赚钱。这里的赚钱指超额收益。

举个例子来说，假设工商银行的股票价格是每股50块钱，那么根据EMH，这每股50块钱的股票价格已经将所有关于工商银行的信息包含了进去，比如该银行的盈利前景，成本估算，行业竞争情况，等等。目前市场上交易的股票价格，是最公平，最准确的价格。

这样的市场被称为是有效的，但市场有效并不代表证券价格就静止不动了。如果有更新的信息被发现，那么证券价格会变化。比如当大家知道工商银行下一季度的盈利会下降时，其股票价格会随之跌落，一直跌到在盈利下降后该股票反应的公平价格为止。

在完全有效的市场下，任何买卖股票的行为都是浪费时间。因为当前的股票价格已经完全反应了所有相关的信息，也就是说其股票价格是公平合理的，不高也不低。如果在这种情况下去买卖股票，相当于赌一个未来的不确定，因为未来的新信息可能是利好，也可能是利空，在这种情况下买卖股票和投一枚硬币猜正反面的性质是类似的。所以对于一个投资者来说，买或者卖都无法给他任何优势，最好的策略是呆而不动(Buy and Hold)，什么都不做。

这样的策略背后的逻辑是什么呢？让我通过下面这个例子来说明一下：

假设你去一个行人拥挤的地铁站里坐地铁。因为地铁有很多道门，因此你可以选择在任何一道门前排队等地铁。每个人都想快点进地铁，在这种情况下大致来说你有两种策略：

1. 主动去寻找最短的队伍并且排在后面。在这种策略下，你需要先望一眼，找到最短的队伍，然后在最短时间呢跑到那里排进去。事实上大部分人确实都是这么做的，当然这种策略也有其风险。最大的风险是你找了半天，后来发现队伍都差不多长，但是因为寻找最短的队伍而浪费了一定时间，到最后反而排在更后面。还有一种风险是你远远看去那个队伍比较短，等你跑到时可能中间已经有人加入了队伍，或者走近一看才发现原来并不是那么短，那么你花了额外的时间寻找那样的队伍并且跑过去，对你来说也是一笔成本。

2. 第二种策略是假定每条队伍都是一样长。每当某个队伍比其他队伍短的时候，总有人会在最短的时间内把那个空给补上。在这种情况下，花时间去找更短的队伍简直就是浪费精力，最合理的策略是看到一条随机的队伍就排上，而不去浪费任何时间寻找更短的队伍。

如果市场是高度有效的，那么第二种策略要比第一种策略更优。因为在第一种策略下，乘客首先需要付出一定的成本（比如花时间找，花力气跑来跑去，等等）去寻找最短的队伍。换句话说，在完全有效的市场下，是没有“免费”的午餐的。

## 半强式有效市场假说(Semi—Strong Form)

无法用公开信息赚钱。

价格已充分反应出所有已公开的有关公司营运前景的信息.这些信息有成交价,成交量,盈利资料,盈利预测值,公司管理状况及其它公开披露的财务信息等.假如投资者能迅速获得这些信息,股价应迅速作出反应。

如果半强式有效假说成立,则在市场中利用技术分析和基本分析都失去作用,内幕消息可能获得超额利润.

比如你在路透社这个新闻机构工作，借用公司内部的便利，你听闻今天下午你们要发布一个关于facebook的巨大丑闻，你也知道当这个新闻发出去的那一刻，facebook的股价就会应声大幅下跌，所以你怎么办？

当然是趁着新闻没发布的时候赶紧把你手里facebook的股票转手卖出去呀！观察一些历史数据就会发现，往往在大型丑闻被报道的几十分钟前，这些作恶的公司的股票就已经开始持续下跌了，这说明什么？说明早已经有内部的人员比我们提前知道了丑闻的发生，并且早已经有人开始抛售相应的股票！

## 弱式有效市场假说 (Weak Form)

用历史信息赚钱。比如股票的 K 线。市场价格已充分反应出所有过去历史的证券价格信息,包括股票的成交价,成交量,卖空金额,融资金融等 。

如果弱式有效市场假说成立,则股票价格的技术分析失去作用,基本分析还可能帮助投资者获得超额利润.

## 总结

市场有效假说背后隐含的逻辑是：很多人做出的判断要优于某些人做出的判断。一个证券的价格，比如工商银行股票的价格，到最后显示50元，是因为市场上有千千万万的交易者，在经过多次交易以后形成了对这支股票价格的共识。这也说明如果你想去购买这支股票，别人不会愿意以49元卖给你，而如果你要卖出这支股票，也没人愿意以51元买入。50元是市场上所有人对这支股票形成的价格共识。在成千上万的投资者说，他们对工商银行股票估值肯定不同，有些人可能会估30元，有些人可能会估100元，但经过股市将这些所有人的估值综合起来以后，形成了50元这个合起来的估计。

那么在资本市场中，众人的智慧程度如何呢？很多有炒股经验的朋友可能会忙不迭举出不少例子，比如1999年的纳斯达克股灾，2008年的金融危机，2015年的中国A股波动，等等，作为众人不理智，其智慧不如个人的例子。这样的想法无可厚非，但却不一定正确。

Fischer Black（对，就是那个Black-schole期权模型中的Black）曾经写过一篇非常有名的文章，叫做Noise。在其中他提到过市场上有两种交易者，Noise trader（噪音交易者）和Information trader（信息交易者）。噪音交易者在进行买卖的时候属于比较非理性的，比如他们听到风吹草，看到新闻报道，或者收到一些小道消息动就忍不住给自己的券商打电话要购买或者卖出自己的股票。而信息交易者则是那些专心分析高质量信息，有能力将噪音剔除的理性交易者。

在这样的情况下，噪音交易者不断买卖，人为的造成市场非理性波动，而信息交易者则有机可乘，可以低买高卖。自然的，如果一个市场中有大多数的噪音交易者，那么这个市场的有效程度就很低，因为大部分人都在瞎买瞎卖，因此资产价格很难反映合理公平的水平。而如果一个市场中有更多的信息交易者，那么这个市场就会趋向有效，因为信息交易者只会在资产价格偏离基本面时进行交易，并且把价格水平拉回到合理的位置。

在文章的最后，Black的结论是：I think almost all markets are efficient almost all of the time. “Almost all” means 90 percent. 就是说，Black认为绝大部分市场在绝大部分时候（90%）都是有效的。虽然有时候确实会有市场无效的时候（比如1999年的科技泡沫），但那还是属于非常少数的情况。

回到文章开头提到的市场有效理论，因为其假设市场的有效性被无数后来者不断质疑。事实上如果我们仔细分析该理论，就会发现它并不是那么不堪一击。从总体上来说，市场有效理论指向的结论可以被细分为两个：

- 市场很难被战胜；（天上不会掉馅饼）
- 市场上的价格总是正确的

市场不可能是完全有效的，存在局部相对弱有效或者无效，这个时候就是套利交易的根本点。


<br/>

# 现代资产定价理论

<br/>

## 参考资料

[量化投资之资本资产定价模型](https://cloud.tencent.com/developer/article/1084689)

现代资产定价模型即 CAPM。

有效市场假说是为了方便研究金融市场而对经济世界做的一个假想，而资本资产定价模型就基于这个假设：

1. 这个模型是干嘛的？

	它是为了研究资本的收益与风险之间的定量关系。

2. 为什么要研究这个？

	我们知道，这个世界有一个基本规律：风险越高、收益越高。当一个产品风险低而收益又高的时候，一定会有大量资金涌入瓜分掉收益。

因此，通过研究资本收益与风险之间的定量关系，我们可以判断这只股票价格的“合理性”，如果风险太大而价格又很低、或者风险太小而价格又太高，说明其价格不合理，有很大变动的可能。此外，还可以估计各种宏观经济变化对股票价格的影响。

## 切点组合、分离定理与市场组合

我们先把前言中说的“研究资本收益与风险之间的定量关系”这个东西放一放。先从金融市场上两个很重要的问题入手——每一天每一刻，投资人们无不在绞尽脑汁思考的两个问题

- 有没有一个收益最大、风险最小的投资组合？
- 它在哪里？

为了搞清楚这个问题，经济学家们简化了这个世界，在这个简化的世界中，经济学家们找到了这个最优的投资组合，它被称为切点组合。现在，我们就来聊一聊这个最优的“切点组合”。

要了解它的原理，我们需要走进经济学家威廉夏普等构建的简化世界：

在这个世界里

1. 所有投资者都是理性的，并且希望财富越来越多
2. 所有投资者都可以及时免费获得充分的市场信息
3. 影响投资决策的主要因素为期望收益率和风险。即他们依据期望收益率和标准差选择证劵（期望收益率最大，标准差最小）
4. 投资者具有相同预期，即有相同的投资行为。也意味着他们对证券的期望收益率、标准差及证券间的相关性有相同预期
5. 市场上存在且只有一种无风险利率
6. 股票市场存在卖空和买空
7. 所有投资者都有相同的投资期限，而且只有一期
8. 股票投资可以无限制的细分
9. 不存在通货膨胀、折现率也不变

## 切点组合

根据假设1、2、3，我们知道投资者将会从所有风险证券组合构成的可行区域（下图的灰色区域）中选择其最优证券组合（灰色区域的外圈黑色沿线）。

根据假设4，我们知道所有投资者的风险可行区域是相同的。如下图所示，纵轴是组合的预期收益，横轴是组合的标准差，标准差反映了组合收益的变动情况，即风险大小，标准差越大，收益变动越大，越不稳定，说明风险越大。

下面的灰色区域就是所有投资者的可行区域，黑色外沿就是最优的证券组合，为什么是最优的呢？以A点为例，在外沿线上面总能找到一个B，它的风险与A一样，但收益更高。

![](/images/quantify/0_0.jpeg)

注意，以上讨论的资产组合，都是有风险的，因为横轴不等于零，在不考虑无风险资产的时候，黑色沿线的组合<b>都是</b>最优组合，但有无风险资产介入情况就有所不同。

根据 假设5 ，市场上只有一种无风险资产C，收益为r0，方差即风险为0，如下图所示。

![](/images/quantify/0_1.jpeg)

- 投资者可以将钱全部投向于某个最优的资产组合，比如B，那么获得收益和承担的风险将全部是rB和σB。
- 同样，投资者也可以将钱全部投向无风险资产，那么获得收益和承担的风险将全部是r0和0。
- 事情当然没有那么极端，更多人把部分钱投资于B、部分投资于无风险资产C，这样形成的组合形成了B-C的连线，在这条线上，任何一个点都是B与C之间的再组合。

那么问题就来了，不考虑无风险资产的时候，黑色沿线上的点都是最优组合，考虑无风险资产，为什么情况就不同？实际上，考虑无风险资产的时候，黑色沿线上有一个最最优的点M，M与C的连线优于M与任何其他组合的连线：

![](/images/quantify/0_2.jpeg)

聪明的你一定看出来了，CM先与黑色沿线相切，相比其他连线，CM上的点可以在相同标准差下获取更高的期望收益。此时，我们果断抛弃除M之外的其他任何风险组合。

至此，我们得到了两个很有用的东西：

1. 切点组合

	切点对应的风险组合，即M点，被称为切点组合。

2. 分离定理

	分离定理也称分离特性，指最优风险组合的确定，与个别投资者的偏好无关。我们也推导过了，无论是谁，只要是理性投资者，他必定会以M作为他的风险投资组合。

### 分离定理有什么意义？

对于从事投资服务的金融机构来说，不管投资者的收益/风险偏好如何，只需找到切点所代表的风险资产组合，再加上无风险证券，就可以为所有投资者提供最佳的投资方案，投资者的收益/风险的偏好，就只需反映在组合中无风险证券所占的比重。

现在，我们回答了投资者们无时无刻不在绞尽脑汁思考的两个问题，的第一个问题

- 有没有一个收益最大、风险最小的投资组合？回答是有的，它就是切点组合。

现在，我们来搞清楚第二个问题

- 它在哪里？

从上面的图中，我们只能知道有这么一个组合M，但是它具体是什么样的组合，具体是那些证券按照什么比例组合成的？这个我们还不知道，现在我们就来把它搞清楚。

从分离定理中我们知道，所有投资者的风险资产都会选择M，没有其他选择，因此M必然是一个“市场组合”——由所有证券构成的组合，在这个组合中，每一种证券的构成比例等于该证券的相对市值。

为什么呢？假设市场上有一种风险证券E，它没有被包括在组合M中，那么肯定没有人持有它，因为所有人都选择了那个最优的M组合，用它来搭配无风险证券。此时这个风险证券E没有需求只有供给，这会使E的价格一直下降，直到人们认为E有利可图并被纳入到组合M中进行持有，此时也许M变成了M’，但这个M’仍然是市场上最最优的风险组合，并与投资者的偏好无关——也只是换个名称而已。至此我们得到了第三个有用的东西：

市场组合

### 市场组合

切点组合即市场组合——由所有证券构成的组合，在这个组合中，每一种证券的构成比例等于该证券的相对市值。

而且上述的分析也说明了市场组合中每一种证券的价格都是均衡价格（在这个价格下，供给等于需求），如果不是均衡价格的话，价格可能高于或低于均衡价格，这是买压或卖压会使价格回到均衡水平。

## 资本市场线

目前我们知道了风险组合中，有一个最最优的组合M，它被称为切点组合，也是一个市场组合，如果它搭配无风险证券，那么我们就可以确定一条有效证券组合CM，CM上的点代表（风险组合M + 无风险资产C）搭配起来的所有有效组合。那么，从C出发，穿过M点的这条射线，就被称为资本市场线。它描述了有效组合的风险与收益之间的关系。

![](/images/quantify/0_3.jpeg)

为什么CM之外的延长线也算上资本市场线？

因为假设中提到没有卖空和买空的限制，投资者可以借入或者贷出无风险资金，投入到切点组合中。举一个例子，对于某风险厌恶的投资者来说，他可能会选择一般投资于无风险资产C，一般投资于切点组合M，那么他会选择有效组合CM1：

$$CM_1 = 0.5C + 0.5M$$

对于某风险厌恶较轻的投资者来说，他可能会借入无风险资产C，连同自己的本金一起投入到切点组合M上，那么他会选择有效组合CM2：

$$CM_2 = -0.5C + 1.5M$$

![](/images/quantify/0_4.jpeg)

经过上面的分析，我们得到了本节的一个重要的概念：资本市场线（CML）

资本市场线描述了有效组合的风险和收益之间的关系。这个关系如何用公式表达？

$$ r_p = r_0 + \frac{(r_M - r_0)}{\sigma_M}\sigma_p$$

- p 是 C M 这条线的其中一个点。

![](/images/quantify/0_5.jpeg)

其中

$$r_M - r_0$$

为M的风险收益（或风险溢价、风险报酬）。

至此，我们了解了有效组合的预期回报与标准差之间的关系。但是，有效组合作为从简化世界中推导出来的一个庞大的组合，它的现实意义在哪里？我们在前言中不是说要研究CAPM模型，用它来分析“某股票收益与风险之间的定量关系，以判断其价格是否合理”的吗？不错，我们前面讨论的

切点组合——分离定理——市场组合（=切点组合）——资本市场线

就是为了研究这个东西做铺垫。

## 证券市场线（资本资产定价模型）

还是要回到单个风险证券上来，对于单个风险证券，有同学要说话要说：

![](/images/quantify/0_6.jpeg)

不（bie）对（sha）的（le），单个证券本身是一个非有效的组合，它始终位于资本市场线的下方。不在资本市场线上，因此不能简单的用标准差来取比例套用这个公式。

![](/images/quantify/0_7.jpeg)

此时我们需要把横轴换一换，考虑单个证券的时候，不再用标准差来度量风险，而是用β系数——β系数反映的是单个证券与整体市场组合的联动性，而不是自己的离散程度。

$$ \beta_i = \frac{\sigma_{i,M}}{\sigma_M^2} $$

换成β系数之后，公式就出来了：

$$ r_i = r_0 + \beta_i (r_M - r_0) $$

那么ri与βi之间的关系就是一条直线了：

![](/images/quantify/0_8.jpeg)

等下，有同学又要说话了。

![](/images/quantify/0_9.jpeg)

![](/images/quantify/0_10.jpeg)

本节至此，我们介绍了几个重要的概念：

### $\beta$ 系数

它反映了单个证券与整体市场组合的联动性，用来衡量单个证券的风险，实际上，我们可以根据β系数来区分股票：

- β>1的股票被称为“攻击性股票”，市场上升时其升幅较大；
- β<1的股票被称为“防御性股票”，市场下降时其跌幅较小；
- β=1的股票被称为“中立性股票”，与市场波动一致，适用于指数基金；

### 证券市场线（SML）

证券市场线是反映单个风险证券收益与β系数关系的直线，注意它与资本市场线（CML）的区别：

- CML关注的市场组合（的收益与风险的关系），而SML关注的是单个证券（的收益与风险的关系）。实际上，不仅仅是单个证券，SML适用于所有有效组合和非有效组合，而CML只适用于有效组合；
- CML用标准差来衡量风险，而SML用β系数来衡量风险。

![](/images/quantify/0_11.jpeg)

### 资本资产定价模型（CAPM）

证券市场线其实就是CAPM的图形形式，它的公式：

$$ r_i = r_0 + \beta_i (r_M - r_0) $$

正是资本资产定价模型CAPM。

## 超额收益形式的CAPM模型

### CAPM模型的贡献

CAPM模型是在一些假设的基础上，推导出了资产的定价模型，通过无风险利率、市场收益率、以及β系数可以得到某种资产的“理论”价格，从而帮助投资者对股票进行评价。此外CAPM模型还剔除了新的风险测度方法，即β系数。在实际中，具有较强的可操作性，如，

- 无风险利率多用短期国债利率或银行业间同业拆利率；
- 市场组合的期望收益率，用上证综合指数，美国可以用S&P500指数；
- $\beta$系数，利用单个股票和市场组合的历史数据可以进行计算，之后再进行显著性检验。

### 超额收益形式的CAPM模型

虽然如此，CAPM模型的缺点也是很明显的，它的成立是基于一连串理想化的假设，这些假设在现实中并不全部成立，这必然使得CAPM的作用大打折扣。

在理想条件下，股票只能获得平均基准收益，而现实中无法满足那些假设要求，股票的收益也不仅只有基准收益，还包括了超额收益，即$\alpha$收益。

$$ r_i = \alpha_i + r_0 + \beta_i (r_M - r_0) + \epsilon_i $$

然后可以转化为

$$ r_i - r_0 = \alpha_i + \beta_i (r_M - r_0) + \epsilon_i $$

此外，$\beta_i$度量股票$i$的风险不用多说，要补充的是它其实是一种系统风险，是不可分散的。$\epsilon_i$是股票$i$的随机误差项，是非系统风险，是可以分散掉的；

## 后记

“单炒股票已经是死路，宏观对冲，大类资产配置才能立于不败之地。这是宏观对冲基金最好的时代。”

<br/>

# 投资组合理论

<br/>

## 参考资料

[MPT 模型的解析解](https://xueqiu.com/8287840120/81867846)

这个不再整理，请参考上面的资料。

“现代投资组合理论”（MPT）是1952年哈里.马克维茨在金融期刊上发表的名为“Portfolio Selection”的文章上提出的投资理论。