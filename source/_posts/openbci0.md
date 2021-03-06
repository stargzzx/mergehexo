---
title: openbci | 基础讲解以及配置
date: 2020-01-05 00:39:34
categories:
- openbci
tags:
- openbci
---
这个是实验室要我做的项目，我们利用的是 openbci 的硬件，这一套设备可以在淘宝上购买。

<!-- more -->


直接在淘宝上搜索 openbci 就可以了，其实物图如下图所示。

![](/images/openbci/0_0.png)

这套设备是采集板 + 无线传输模块。

其采集原理是，让电脑和无线模块同处于一个局域网内，然后，采集板采集数据，通过无线模块就可以传输到电脑上了。

<br/>

# 配置无线模板

<br/>

所以，这里面有一个前提是要配置无线模块。

![](/images/openbci/0_1.jpg)

上图就是无线模块，首先，我们先打开给无线模块充上电，然后打开。

首先，无线模块自己本身就是一个无线发射器，所以，其有无限端口，我们假设这个无线的名字是 openwifi-3232。

无线模块通上电之后会搜索之前已经连接过的无线，所以，其在开机的时候会在会看见 led2 闪烁4次，0.5秒一次，这就是说明，无线模块正在搜索，假设，已经搜索到原来的无线，那么其连接上去后， led 2 就会稳定下来。

如果没有找到无线模块，那么就会在 2 秒内快闪 10 下，并且，暴漏出 openwifi-3232 用来配置无线信息。

一般使用手机连接这个无线，因为用电脑连接可能会导致配置信息错误，另外，一个非常注意的的点就是，配置 无线模块信息的时候，千万不能让无线模板和采集器连接，这样会导致不能配置信息，切记。

用手机连接后，我们访问下面的 ip 地址

	192.168.4.1

这个时候会出现一个界面。（由于这个无线模块非常廉价，所以，会出现很多让人抓狂的事情，比如反应慢、动不动就断开链接，总之耐心一点）

![](/images/openbci/0_2.png)

如果你之前配置过无线，就会有红框中的内容，这个是清除之前的配置信息的。点击这个后，反应很慢，然后我们再点击最上面的

	click to configure wifi

出现下面的界面

![](/images/openbci/0_3.png)

点击第一个，这个选项是扫描周围的无线，第二个选项是不扫描，建议第一个。

由于无线模块的配置很低，所以，在扫描的时候会花费大量的时间，这一步要耐心等待。

<br/>

# 可能会出现的错误

<br/>

## 局域网的名字不一样

我遇到了一个非常奇怪的问题，就是我的局域网是 H3C ，但是，我电脑上显示这个局域网是 H3C 2，脑电的无线模块是 H3C ，尽管连接的同一个局域网，但是由于局域网名字的不同导致不能使用。

最后，我用手机开了一个热点，进行连接成功。

## 有的电脑运行不了

首先你要关闭防火墙、各种杀毒软件。如果还出现这个问题，那么只能换一台电脑。

还是我，遇到了让我很崩溃的问题，就是，有一台电脑之前可以连接，但是后来就不能连接了，迫不得已，只能换一台电脑。

<br/>

# 采集板 + 无线模板

<br/>

根据阵脚的位置，连接这两个板子，要尤为注意的是，这两个板子耗电量非常厉害，如果不使用，请把电池拔下来。

如果电池的电量不足，就出现 led 闪烁的情况，如果电量不足，是不能够采集的。

![](/images/openbci/0_4.png)

将电池连接好，通上电后，我们首先要打开 openbcihub 这个是前置的软件。

反正，无论是自己写代码还是打开它给的软件，第一步就是打开 openbcihub 软件。

然后，我们再打开 openbci_gui 软件，千万要记住，这个步骤不可以错。

打开后，出现下面的界面

![](/images/openbci/0_5.png)

这个软件会自己 searching 无线，出现后，选择就好了，更多具体的细节你可以看官方给你的文档，也就是淘宝给你的中文文档。

因为这个软件的使用不是重点，在下一篇我将重点介绍，如何使用 python 来获取脑电采集信号。