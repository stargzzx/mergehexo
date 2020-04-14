---
title: 币圈 | 永续合约将如何影响你的投资
date: 2020-04-10 17:21:16
categories:
- virtualcoin
- 基础
tags:
- virtualcoin
---
长期以来，期货固有的特质如合约交易、交割日注定它是一个最被容易操控的工具。合约交易因为只是交易一张合约而非比特币物，所以搬砖大军无法去套利烫平价差；有交割日限制便决定在交割时，无论亏损还是盈利，都必须平仓承认损失或者了结收益，且最后账户盈亏参考的结算价格必须是现货价格。因为现货价格有搬砖大军限制及全球投资者充分博弈的公允价格，很难被人为操控，所以在这一天操纵期货价格，达到目的后在合约交割前平仓走人成为挽回损失的大户或者扩大利润的大户最直接的手段。尤其是在交易量相对不大的平台、交易量不大的时间段更容易被人为操控。另一方面，合约被交割后，投资者的委托挂单也会被取消，所以，新的合约刚上市，市场深度会严重匮乏，这个时候操作价格成本也非常低。为了解决这些弊端，永续合约的推出便变的迫在眉睫。

<!-- more -->

## 什么是永续合约？

永续合约是期货合约的衍生品，和期货一样，它是合约交易，不是现货交易，你买入后，不会得到数字货币。交易模式和期货一样也是保证金交易。最大的不同是没有交割日。即你开的仓位只要没被爆仓强平，永远不会被动平仓，你的委托挂单只要不是主动撤回，会永远保留直到达成交易。通俗的讲，就是一个可以永远交易的期货合约。这个模式从根本上解决了上文中提到的临近交割时或刚交割后出现的价格操纵。但更大问题来了，如何保证合约价格和现货价格的价差一致？因为期货合约有交割日，所以越临近交割日，期货价格要向现货价格收敛，最终保持一致。而永续合约没有交割日，那期货价格便没有了约束，将变成一个纯赌博的工具了。

![](/images/virtualcoin/17_0.jpg)

![](/images/virtualcoin/17_1.jpg)

为了维护游戏的公平性，这个时候必须引入了一个资金费用去锚定现货价格，即在某一时刻当永续合约价格与现货价格偏离合理价差，资金费用把这个偏离价差强行拉回至合理价差！具体操作怎样的呢？

<b>在某一时刻，当期货价格大于且明显偏离现货价格，多头需要付费给空方，在某一时刻，当期货价格小于且明显偏离现货价格，空方需要付费给多方。偏离越大，费率越高！</b>

例如，根据历史统计，比特币期货价格与现货价格价差在 -50—50 点之间，有个大户把永续合约价格恶意拉升，把价差扩大到200点，如果在某一计费时刻，价差还仍然保持在合理价差之外，不好意思，你得赔偿持有空单投资者的损失，这个时候系统会从你的已实现收益中扣除，拉的越高，赔偿越大！由于计费是根据合约总价值计算而非保证金计算，所以仓位越大，计费越高！

![](/images/virtualcoin/17_2.jpg)

这样，通过费用机制，牢牢的把永续合约价格锚定注现货价格，最大可能去限制价格的恶意操纵。但是需要注意的是，系统不会实时去监控价差，一般会在一天中设几个特定的时间点，在那个时间点如果价差依然偏离过大且持仓仍然存在，才会启动该机制。这样也衍生了一个套利策略：如果当永续合约价格出现异常，比如，大幅下跌，而现货价格并未马上跟随，此时套利者可以在永续合约上建立多头头寸，并同时卖出等价值的现货，等待价差回归合理水平，套利者除了可以挣取价格收敛带来的价差，还可以得到一笔丰厚的、来自于空头的补偿金！

ps:我不是很理解为什么卖等价值的现货可以挣取价格收敛带来的价差这个关系。我个人猜测是，由于合约出现做空者过多，导致，现货价格高于永续合约，所以，市场情绪为空。这个时候，你可以卖出相应的货币，然后当市场价格收敛，也就是价格下降，你就可以获利。不过，对于，我自己的这个解释，我有两点疑问。

- 永续合约和现货的走势应该不是强相关吧，即便是合约情绪为空，那现货也不一定下降吧
- 即便是相关，那么现货价格收敛的时候，可能价差就在合理范围内了，那么，空头也不需要向多头支付吧

不过，如果比特币后市单边看涨，价差会长时间大于合理价差，多头在大多数时候需要付给空头费用；如果比特币后市单边看跌，则反之。

