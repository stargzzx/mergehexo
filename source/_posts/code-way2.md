---
title: 编程之道 | 设计模式和数据结构的合理运用
date: 2020-03-17 16:09:21
categories:
- 编程之道
- 自身
tags:
- 编程之道
---
近期学了设计模式，我将其运用在公司量化项目的数据库数据的生成脚本中。

额，装个逼，当我对之前的脚本进行修改之后，我就知道我的编程水平确实又提高了。

年少装逼，要与何人说？写完代码，空环后，无人太凄凉，只剩脊椎隐隐痛！！！

半小时之后：

>经过我再次复查代码，发现根本不需要工厂模式和单例模式，额，下面记录了我打脸的过程。
不过这特说明了写博文的好处，可以将自己的代码进行复查，去掉错误的思想。

<!-- more -->

<br/>

# 内容亮点

<br/>

- 将 6 个 for 循环变成 3 个的数据结构
- 工厂模式
- 单例模式

<br/>

# 参考资料

<br/>

上述相关内容的教程，你可以我下面的博文中找到。

[设计模式 | 单例模式](https://benpaodewoniu.github.io/2020/03/14/designthinkings4/)
[设计模式 | 工厂模式](https://benpaodewoniu.github.io/2020/03/14/designthinkings5/)

<br/>

# 将 6 个 for 循环变成 3 个

<br/>

这里面的重点就是用了 dict 这个数据结构。

想象一下有这样两个数据结构

	a = {
		'binance':['btc'],
		'coinbase':['btcusd,btcusdt']
	}
	b = {
		'coinbase':['eth']
		'binance':['ethusd']
	}
	c = {
		'coinbase':1,
		'binance':2
	}

这里只是一个简单的例子，真正的例子非常复杂，处于各种原因 a,b 中记载了各种无序 symbol ,出于其他要求 a b 里面的顺序和数据是不一样的，c 中记载了所有 gateway 的 sort。

现在有一个要求就是在数据库列表中将相同 coin 的币种根据 c 的顺序递增的排列。

- coin

coin 的意思就是币种，对于 a 来说 coin 是 btc ，对于 b 来说 coin 是 eth。

- 举个例子

a 中的例子顺序应该是 coinbase: btcusd->1 | btcusdt-> 2 | binance: btc->3

因为 a 或者 b 的元素非常的多，如果按照我之前的编写代码的模式，我会大量使用 for

比如

```python
# 使用 i 暂存币种的顺序
i = 0
for gateway,symbols in a.items():
	for symbol in symbols.split(','):
		for _gateway,sort in c.items():
			if gateway == _gateway:
				i += 1
```

这里面用了 3 个 for 循环，但是，根据我的项目需求，这样写的话，我还得再嵌套 3 个 for 循环，最后是 6 个 for 循环。

稍微有点知识的人就知道这个代价是非常惨重的。

所以，我们为什么不事先把顺序先存起来呢？

主要利用的是下面的这个结构体

	dict

因为上面的某些排列具有唯一性，及

	binance.btc | coinbase.eth

所以我们使用一个 dict 将数据预先处理，存起来

	dict[coin][gateway_symbol] = sort_number

- coin : 币种信息 btc
- gateway_symbol : binance.btc
- sort_number : 排列顺序

然后，我们想要取那个固定的 symbol 的 sort_number 的时候只需要

	dict[coin].get(gateway + '.' + symbol)

<br/>

# 工厂模式

<br/>

由于，我们的环境是两个

- 测试
- 正式

所以，我们的代码如下

```python
t = 'test'
if t == 'official':
    dockersql = DockerSql(symbol, instance_id=instance_id, key_type=symbol)
    dockersql.key_type = symbol
else:
    dockersql = DockerSql(symbol, instance_id=instance_id, key_type='test')

# 由于使用了单例模式，所以需要再次赋值
dockersql.instance_id = instance_id
dockersql.generate_tables()
```

通过 t 来初始化不同的实例。

<br/>

# 单例模式

<br/>

最开始的时候，我们的代码如下

```python
coins = {
    'BTC': ['BTC1', 'BTC2', 'BTC3', 'BTC4', ],
    'ETH': ['ETH1', 'ETH2', 'ETH3', 'ETH4', ],
    'BCH_BSV': ['BCH_BSV1', 'BCH_BSV2', 'BCH_BSV3', 'BCH_BSV4', ],
    'TRX_XRP': ['TRX_XRP1', 'TRX_XRP2', ],
    'LTC_EOS_ETC': ['LTC_EOS_ETC1', 'LTC_EOS_ETC2', ],
    'BNB_HT_OKB_HPT_FTT_BTMX': ['BNB_HT_OKB_HPT_FTT_BTMX', ]
}

for coin, symbols in coins.items():
    for symbol in symbols:
        dockersql = DockerSql(symbol, key_type=symbol)
        dockersql.generate_tables()
```

这样的话，你可以看到需要创建大量的实例，但是，这些实例差别并不大，只是输入的参数不一样。

所以在 DockerSql 这个 class 中增加这样一个方法

```python
def __new__(cls, *args, **kwargs):
    if not hasattr(cls, 'instance'):
        cls.instance = super(DockerSql, cls).__new__(cls)
    return cls.instance
```

然后，修改上面的代码如下：

```python
coins = {
    'BTC': ['BTC1', 'BTC2', 'BTC3', 'BTC4', ],
    'ETH': ['ETH1', 'ETH2', 'ETH3', 'ETH4', ],
    'BCH_BSV': ['BCH_BSV1', 'BCH_BSV2', 'BCH_BSV3', 'BCH_BSV4', ],
    'TRX_XRP': ['TRX_XRP1', 'TRX_XRP2', ],
    'LTC_EOS_ETC': ['LTC_EOS_ETC1', 'LTC_EOS_ETC2', ],
    'BNB_HT_OKB_HPT_FTT_BTMX': ['BNB_HT_OKB_HPT_FTT_BTMX', ]
}

dockersql = DockerSql(symbol, key_type='')

for coin, symbols in coins.items():
    for symbol in symbols:
        dockersql.key_type = symbol
       	dockersql.generate_tables()
```

这样就可以一直使用一个对象了。


<br/>

# 写在最后

<br/>

当我验证代码的时候，发现我之前的单例模式写错了，经过修整后，发现根本不需要工厂模式，但是，工厂模式作为一个很好的东西，所以，我还是添加了。


<br/>

# 再次写在最后

<br/>

我发现单例模式也不需要，即便是去掉

```python
def __new__(cls, *args, **kwargs):
    if not hasattr(cls, 'instance'):
        cls.instance = super(DockerSql, cls).__new__(cls)
    return cls.instance
```

直接修改实例里面的属性的话，也是一样的。

```python
coins = {
    'BTC': ['BTC1', 'BTC2', 'BTC3', 'BTC4', ],
    'ETH': ['ETH1', 'ETH2', 'ETH3', 'ETH4', ],
    'BCH_BSV': ['BCH_BSV1', 'BCH_BSV2', 'BCH_BSV3', 'BCH_BSV4', ],
    'TRX_XRP': ['TRX_XRP1', 'TRX_XRP2', ],
    'LTC_EOS_ETC': ['LTC_EOS_ETC1', 'LTC_EOS_ETC2', ],
    'BNB_HT_OKB_HPT_FTT_BTMX': ['BNB_HT_OKB_HPT_FTT_BTMX', ]
}

dockersql = DockerSql(symbol, key_type='')

for coin, symbols in coins.items():
    for symbol in symbols:
        dockersql.key_type = symbol
       	dockersql.generate_tables()
```

所以，最后的结论便是，工厂模式虽好，但是，也不要贪杯奥。
