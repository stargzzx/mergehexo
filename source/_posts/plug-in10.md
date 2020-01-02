---
title: QQ 机器人
date: 2019-03-17 10:05:03
categories:
- plug_in
tags:
- plug_in
- 脚本
- QQ
- 机器人
- QQ机器人
---
最近一段时间一直在搜集相关资料，现在将搜集到的结果给大家分享一下。
<!-- more -->
我们团队想做一个关于淘宝优惠券的机器人，我负责 QQ 端，因为微信端有 itchat ，但是 QQ 端并没有这种库，于是我就展开了漫漫的寻找之路。
## 产品说明
可以收私人和群消息
收到消息后能根据合理的消息诉求来发送信息
## 产品 1 基于模拟器
打算开一个模拟器，然后用 airtest 和 popc 库来进行获取信息。
但是，这两个库我并不能发送私人消息，而且，如果用模拟器的话，局限性太大。
第一是它只能单窗口爬取信息，做不到多窗口爬取。
第二是如果后期要不部署到服务器端，那么开模拟器的负荷太大，最好让 QQ 进行无界面登录。
## 产品 2 基于 QQ 5.8 协议
[下载地址](https://download.csdn.net/download/a1311817771/10929605)
经过多方寻找，终于在 CSDN 上找到，一个 python 写的脚本，可以进行脚本登陆。
可以实现发送信息和群消息，但是代码非常难读，并且，询问了作者只能发消息，不能接收消息。
他的代码是利用 python 模仿真机，然后进行登陆，发送消息，也是模仿的事件发送。
其，无界面登录实现了，但是，由于是脚本模仿真机，所以，收消息没有做，可能能写出来，但是，我目前的水平达不到。
## 产品 3 基于酷Q nonebot SDK
经过大海的提醒，我开始看酷Q ,发现其有很多插件。
>酷Q，是一款基于Smart协议功能强大的机器人软件，它可以通过安装插件实现自动审核他人申请入群、自动踢人、自动管理群等自动化操作，还能实现自动群聊、自动聊天，起到活跃群组气氛的重要作用，节省您的宝贵时间。

然后经过一番寻找，发现前人已经写了一个完整的 SDK。
也就是 nonebot
[CoolQ HTTP API 插件](https://github.com/richardchien/coolq-http-api)
[基于 酷Q 的 Python 异步 QQ 机器人框架](https://github.com/richardchien/nonebot)
[这个插件的API](https://none.rclab.tk/guide/)
[配置方法](https://none.rclab.tk/guide/installation.html)
nonebot 是在酷Q 的接口上进行二次开发。但是，开发的过于复杂，里面的命令都必须要有严格的格式化。
比如，你要查天气，在 QQ 上输入：

	天气 西安
	
你必须得有前置，否则不能识别消息。
虽然这个 SDK 已经满足大多数的要求，但是，用户如果这样发格式化消息的话，用户体验非常差。
而且，淘口令这种东西其很难做到有固定的前缀。所以，虽然这个功能很强大，但是用不了。
我急需一种，用户给我发来信息，我自己处理。
## 产品 4 基于酷Q aiocqhttp SDK
[CoolQ HTTP API 插件](https://github.com/richardchien/coolq-http-api)
[CQHttp Python SDK with Asynchronous I/O](https://github.com/richardchien/python-aiocqhttp)
这个 SDK 几乎能满足我所有的要求，可以进行收发信息，但是依附于酷Q 客户端。好在，酷Q 的 air 并不大，很轻便，关键是免费。
下面我就来说说这个插件的具体细节。
首先是 酷Q 和 pro 的区别。
{% img /images/plug_in/10_0.png %}
所以，我们的 aio 在功能上还是有限的，但是开发一个简单的机器人也是可以了。
截止到我写这篇 blog ， pro 的价钱是半年 60 块。
在这里我只想说明这个 SDK 是建立在 CoolQ HTTP API 插件上的。
这个 SDK 只是对这个插件的简单封装。所以，我们可以看插件的 doc 。
[SDK文档](https://github.com/richardchien/python-aiocqhttp)
在 sdk 的 doc 中说的已经很详细了，我主要说说可能的疑点。
在 SDK 所给的例子中，有这样一段代码。
{% codeblock %}

@bot.on_notice('group_increase')
async def handle_group_increase(context):
    await bot.send(context, message='欢迎新人～',
                   at_sender=True, auto_escape=True)

{% endcodeblock %}
在上面这段代码中，@bot.on_notice('group_increase') 中的 group_increase 来源于插件。
具体位置是。
[上报信息类型](https://cqhttp.cc/docs/4.8/#/Post)
如
{% img /images/plug_in/10_1.png %}




































