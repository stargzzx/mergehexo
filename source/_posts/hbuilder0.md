---
title: hbuilder | 在 macbook 用一段时间就出现闪退
date: 2019-12-07 22:52:39
categories:
- hbuilder
tags:
- hbuilder
---
问题图题目所示，这是这个软件在很新的 mac 上的 bug，解决方法看博文。

<!-- more -->

[windows请参考](http://ask.dcloud.net.cn/article/35583)
[macbook参考](https://ask.dcloud.net.cn/article/35756)

Mac上 HBuilderX起不来，主要有两种：

	新下载的起不来，请参考第一章
	以前可以启动，后来起不来，请略过第一章，直接看第二章

<br/>

# 第一章

<br/>

新下载的HBuilderX无法启动

近来，部分小伙伴反馈，刚下载的HBuilderX，安装后，点击启动，在程序呜弹跳了几下，闪退了。

	解决方案：重启电脑

<br/>

# 第二章

<br/>

以前可以启动，后来无法启动

有可能是配置文件损坏了，直接删除配置文件即可。

配置文件目录：

	$HOME/Library/Application\ Support/HBuilder\ X

**请注意：** mac上，如果路径包含空格，需要\进行转义

删除损坏的配置文件，在命令行输入

复制代码

	rm -rf $HOME/Library/Application\ Support/HBuilder\ X

如果还是不行，重启电脑试试

<br/>

# 特别说明

<br/>

某些mac电脑，确实存在需要重启才能打开的情况。内部正在排查、优化。给大家造成的不便非常抱歉