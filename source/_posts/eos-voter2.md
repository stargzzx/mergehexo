---
title: eosvoter|dfuse接口对接，获取节点的reward
date: 2019-11-25 17:29:21
categories:
- eos_voter
tags:
- eos_voter
- 项目经验
- 币币生息
---
因为之前获得 reward 的接口挂了，官方换成其他的了，所以，我们也要重新对接相关的接口。

这里说一下 reward 就是出块奖励和投票奖励。

<!-- more -->

# 参考链接
[bloks.io](https://bloks.io/)
[dfyse](https://app.dfuse.io/)

# 官方获取

根据抓取 bloks.io 的数据来说，其 receive token 的包 url 是

{% img /images/eos_voter/2_0.png %}

拿到 request 的 url 进行解析得到

	https://www.api.bloks.io/dfuse?type=search_transactions&q=(auth:blockpooleos OR
						  receiver:blockpooleos OR
						  data.to:blockpooleos OR
						  data.from:blockpooleos OR
						  data.receiver:blockpooleos) data.to:blockpooleos&options[sort]=desc&options[cursor]=&options[limit]=25&options[withReversible]=true

尤为注意的一点是，我们这个url是点击 receive token 获取的，也就是选择了

{% img /images/eos_voter/2_3.png %}

如果我们没有选择这个选项，就会有大量杂乱的数据

{% img /images/eos_voter/2_4.png %}

且不选择这个选项的url 是

	https://www.api.bloks.io/dfuse?type=search_transactions&q=(auth:blockpooleos OR
						  receiver:blockpooleos OR
						  data.to:blockpooleos OR
						  data.from:blockpooleos OR
						  data.receiver:blockpooleos)&options[sort]=desc&options[cursor]=&options[limit]=25&options[withReversible]=true


是没有

	data.to:blockpooleos

这个条件的，这一点要尤为注意。

# dfuse 接口接入

我们知道了数据来源于 dfuse 那么我们要到 dfuse 中探寻如何请求相关的数据。

点击参考链接的 dfuse 进入。

我们先了解一下价钱

{% img /images/eos_voter/2_1.png %}

从图中可以看出，免费的一个月内可以有 250k 次请求，对于我们目前的项目来说完全够用了，所以，我们直接注册就好了，在这里，我是拿我的 github 账号登陆的。

登陆之后，申请一个 API_KEY。

{% img /images/eos_voter/2_2.png %}

在这里我们先说明一下，我们申请的这个 api_key 是永久的，但是根据这个 key 所产生的 JWT（我称之为令牌）的寿命只有24小时，也就是，我们验证的时候是用

JWT来获取数据的，而 JWT 是通过 key 来得到的，但是，JWT只有24小时的寿命。

更详细的内容，你可以点击图中画红框的地方。（这里最好切换成中文，英文这里出错）

[文档](https://docs.dfuse.io/)

找到 eosio

{% img /images/eos_voter/2_5.png %}

选择 Getring started

然后

{% img /images/eos_voter/2_6.png %}

进入我们的验证方式。

[Authentication](https://docs.dfuse.io/guides/core-concepts/authentication/)

这里的话你可以仔细看看，但是，其核心点就是，我们要通过 api_key 来获取一个 JWT ，然后，我们拿着这个 JWT 来请求数据，但是，一个 JWT 的寿命只有 24 小时。

另外，值得注意的是，官方也特别提到，一个好的请求，在 24 小时内 JWT 应该是不变的，所以，你要保存一下你的可用 JWT。

我在我代码中用一个 txt 文件保存，并且，我们生成 JWT 的时候，也同样会得到这个令牌的过期时间，我在项目中使用 txt 文件来保存，当然，瑞哥建议我用内存保存，但是，由于某些方面的限制，我一直用的是硬盘文件保存在，所以，有时间研究一下内存保存。

在 eosvoter 中，使用 api_key 得到 JWT

{% img /images/eos_voter/2_7.png %}

根据时间，看是否更新JWT

{% img /images/eos_voter/2_8.png %}

得到相关的 token ，这个 token 就是 JWT ，来获取数据

{% img /images/eos_voter/2_9.png %}

至此，dfuse 接口就对接完毕了。

对了，还有关于这个接口的 api 你可以参考

{% img /images/eos_voter/2_5.png %}

中的

API Reference

{% img /images/eos_voter/2_10.png %}



