---
title: coinex| 整体流程展现 版本一
date: 2019-11-25 14:16:17
categories:
- coinex
tags:
- 币币生息
- 项目
- coinex
---
这个非常重要，当然，目前我只是明白了如何对接交易所接口而已，对引擎的整体流程并不了解。

以后，我会继续补全这个博客。

<!-- more -->

整个项目目前我认为由三个部分组成，第一是策略层，第二是接口层，第三是引擎层。

# 策略层

策略层就是我们要执行的操作，比如什么时候买，什么时候卖等，但是，目前我的任务是让项目跑起来，所以，当前的策略是对接好下单、撤单、订单状态改变通知、余额等。

# 接口层

接口层是和交易所进行对接，也就是实现诸如下单、撤单的功能。为什么要有接口层呢？是因为，每一个交易所都有自己的一套体系和返回值，而，我们要把每一个交易所的差异进行抹平，返回的状态和信息要与我们的coinex相吻合。

# 引擎层

引擎层，是连接接口和策略的中间者，起到控制、传递信息、触发事件的作用。

就目前我理解的引擎层是这样的。我们对接好交易所接口后和相应的策略后。当交易所的波动达到我们所定制的策略阈值后，会触发策略层的事件，然后，将这个事件信息通过引擎层进行传递，并且调用相应的接口层的代码。

coinex 的引擎层有很多，由时间引擎、时间引擎、主引擎等等，据瑞哥告知，其是事件驱动。

但是，我理解的还不够好，所以，还不能完全解读，就目前而言，我只能写一下策略层和接口层的逻辑。

# 代码逻辑

目前，我们的操作只是针对于合约。

我在后面也会把每一个我经手过的交易所接口进行单独的发放，但是，现在，我不针对单独的交易所，只是广义上说明一下流程。

根据我上面的文章

[币币生息|数据库设计](https://benpaodewoniu.github.io/2019/11/19/coinex0/)

我们把交易所的 API 和 secret 填入到相应的表中。

我们的入口函数是 main 函数。

{% img /images/coinex/2_0.png %}

整个项目是由多个线程并行工作的，而引擎层就负责调度这些线程。虽然，下面是以线程来命名，但是，我们不一定是线程来运行下面的，我们是根据场景来的。

# 线程 1

我们以 coinex 为例

	GATE_WAY_MAP = {
		# 'fcoin': FcoinSpotGateway,
		'gate_swap': GateSwapGateway,
		'fmex': FmexSwapGateway,
		'bybit': BybitSwapGateway,
		'coinex':CoinexSwapGateway,
	}

经过一番查询，我们最终会得到 GATE_WAY_MAP[platform] = CoinexSwapGateway

	main_engine.add_gateway(GATE_WAY_MAP[platform])

然后，我们由上面的代码跳到

	coinex\alpha\trader\engine.py
		add_gateway 函数

{% img /images/coinex/2_4.png %}

我们根据其中的一个代码

	 gateway = gateway_class(self.event_engine, self.timer_engine)

跳到相应的接口文件中，对于 coinex来说，就是

	coinex\alpha\gateway\coinex\coinex_gateway.py 中的
		class CoinSwapGateway

关于 coinex 的接口文档，我们会专门写一期的。

在上面的那个线程会初始化相应的 gateway。

这个初始化是初始对应的 rest_api 和 ws_api。

然后，程序继续执行，我们会执行到

	main_engine.connect(settings, platform.upper())

这个函数会跳到

	coinex\alpha\trader\engine.py
		connect() 函数

{% img /images/coinex/2_5.png %}

而，我们得到相应的 gateway 之后

	main_engine.connect(settings, platform.upper())

跳到对应接口层的 connect 函数，对于 coinex 来说就是跳到

	coinex\alpha\gateway\coinex\coinex_gateway.py
		的CoinexSwapGateway类下的 connect 函数

{% img /images/coinex/2_6.png %}

而，我们可以看到这一层的 connect 也同样调用了对应 rest_api 和 ws 的 connect ，每一个 connect 都有相对应的作用。

在这里我们稍微讲一下 coinex 的 rest_api 的 connect 做了什么把。

下面是 

{% img /images/coinex/2_7.png %}

这段代码中有一个是

	self.query_contract()

{% img /images/coinex/2_8.png %}

这个是拿到相应交易所的合约信息，这个合约信息将在下面那个线程中有用。此时，我们会在控制面板上输出：

{% img /images/coinex/2_9.png %}

然后，我们的main 函数会继续执行下去，到达

	cta_manager.add_strategy(strategy_name, _setting)

这个内容我们从线程2开始。

# 线程 2

	cta_manager.add_strategy(strategy_name, _setting)

开始，由这句话，我们跳到

	coinex\alpha\app\cat_strategy\cat_manager.py

{% img /images/coinex/2_1.png %}

我们由上面的

	is_connect, is_contract = self.is_connect(vt_symbols)

跳到

	coinex\alpha\app\cat_strategy\cat_manager.py

{% img /images/coinex/2_2.png %}

我们要连接某一个交易所的信息的话，就首先要先得到其交易所有什么合约信息，拿 coinex 来说。

{% img /images/coinex/2_3.png %}

我们从图中可以看出，coinex 是有三个合约的，而，我们首先就是要拿到每一个交易所的合约信息。

根据下面的代码：

	contract = self.main_engine.get_contract(vt_symbol)

如果在线程 1 中的合约信息传递过来，根据

{% img /images/coinex/2_1.png %}

我们的控制台将输出：

{% img /images/coinex/2_10.png %}

根据这张图片

{% img /images/coinex/2_1.png %}

我们拿到合约信息后会继续执行

	self.cta_engine.add_strategy(
		class_name, strategy_name, vt_symbols, setting
	)

这句话会跳到

	coinex\alpha\app\cat_strategy\engine,py 的564 行
		add_strategy 方法

这个方法会做两个任务（目前，我已知的业务），一个是初始化策略层另外一个就是注册事件。

{% img /images/coinex/2_11.png %}

## 初始化策略层

策略层是在 579 行执行的

	strategy = strategy_class(self, strategy_name, vt_symbols, setting)

这句话将会初始化策略层，并且返回策略层的 strategy ，比如，对于 coinex 来说

这句话会跳到

	coinex\alpha\app\cta_strategy\strategies\coinex_strategy.py

执行初始化

{% img /images/coinex/2_17.png %}

然后，我们会再次返回回来，并将这个 strategy 进行添加，也就是

{% img /images/coinex/2_11.png %}

中的

	580 行的
	self.strategies[strategy_name] = strategy

我们在 debug 中可以看到

	strategy为
		<alpha.app.cta_strategy.strategies.coinex_strategy.CoinexStrategy>

## 注册事件

继续执行到第 586 行

	self.register_event(vt_symbols)

会跳到 135 行的 register_event 函数

{% img /images/coinex/2_12.png %}

这里注册各种事件和事件回调，比如其中一句话：

	self.event_engine.register(EVENT_POSITION + vt_symbol, self.process_position_event)

这个是仓位函数，其注册的事件是

	EVENT_POSITION + vt_symbol
		在这里我们假设是 EVENT_POSITION + vt_symbol 等于 ePosition.COINEX

在这里我们要特别提一下，我们接口的注册事件一定要和这个的注册时间保持一致，拿 coinex 的 position 来说明

在接口层 

	coinex\alpha\gateway\coinex\coinex_gateway.py
		中的 on_position

{% img /images/coinex/2_15.png %}

其有代码

	self.gateway.on_position(position)

跳到

	coinex\alpha\trader\gateway.py

{% img /images/coinex/2_16.png %}

我们一定要保证

	self.on_event(EVENT_POSITION, position)
	self.on_event(EVENT_POSITION + position.vt_symbol, position)

这两行代码和上面的注册事件保持一致采用，具体的为

	EVENT_POSITION == EVENT_POSITION + vt_symbol == ePosition.COINEX
	或者
	EVENT_POSITION + position.vt_symbol == EVENT_POSITION + vt_symbol == ePosition.COINEX

一定要保证两个注册事件的一致，否则不会传递，调用。

然后这个注册事件回调 process_position_event 方法

{% img /images/coinex/2_13.png %}

而其中最后一句话

	self.call_strategy_func(strategy, strategy.on_position, position)

就是执行策略层的 on_position 函数，在这里先 pull 一下策略层的 on_position 函数截图

	coinex\alpha\app\cta_strategy\strategies\coinex_strategy.py

{% img /images/coinex/2_14.png %}

# 线程 3

main 函数继续执行，将会执行

	cta_manager.start_strategy_all()

这句话就是执行之前得到的策略，对于 coinex 来说，就是执行

	coinex\alpha\app\cta_strategy\strategies\coinex_strategy.py

后面就是根据策略层里面的步骤，来调取各个策略了。

我们将会在不同的交易所对接中，详细的介绍各个步骤。