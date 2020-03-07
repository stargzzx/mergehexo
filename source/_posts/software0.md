---
title: 科学上网（翻墙软件）
date: 2018-06-13 15:10:51
categories:
- software
tags:
- xx-net
- VPN
- 翻墙
- software
---
这是一篇教你如何科学上网的教程。

<!--more-->

我用过 N 多的翻墙软件，用过收费 green ，说实话一般般，经常断。

用过免费的各种翻墙软件，但是都不稳定，直到在偶然的情况下我发现了一款免费的软件，并且开源。

它就是 xx-net。

它之所以好，是因为它小巧方便，不仅有电脑端还有手机端，但是貌似现在只有安卓的。

而且，操作简单，只需要你的电脑有浏览器就好。

很稳定，有时候我看 YouTube 都不卡，但是，平时我也就用来查资料，所以完全满足我的要求。

登录 github，然后搜索 xx-net，进入排名第一的，再根据上面的流程做就好了。

下面我再说说我所遇到的问题吧。

## 申请私用 ID 失败

关于私用 ID 的介绍，可以看下面。

{% codeblock %}
XX-Net/code/default/gae_proxy/server/

Windows 用户
	在资源管理器打开 server 目录(就是这个文件所在的文件夹)。
	在资源管理器的空白处 Shift + 右键，在弹出的菜单里选择"在此处打开命令行窗口"。
	输入下面的命令来部署服务端：
	uploader.bat <appids> [-debug] [-password rc4_password]

	例子：
	部署 xxnet-1|xxnet-2|xxnet-3 这三个 appid
	uploader.bat "xxnet-1|xxnet-2|xxnet-3"
	开启调试输出并部署 xxnet-1 这个 appid
	uploader.bat "xxnet-1" -debug
	使用 123456 作为 RC4 密码并部署 xxnet-1 这个 appid
	uploader.bat "xxnet-1" -password 123456
	开启调试输出，使用 123456 作为 RC4 密码并部署 xxnet-1 这个 appid
	uploader.bat "xxnet-1" -debug -password 123456
	uploader.bat "lxxnet-1|lxxnet-2|lxxnet-3|lxxnet-4|lxxnet-5|lxxnet-6|lxxnet-7|lxxnet-8|lxxnet-9|lxxnet-10|lxxnet-11|lxxnet-12" -password 123456
{% endcodeblock %}

## APPID的流量用完

一般公用的 ID，是不支持你看视频的，因为大家都在用公用 ID ，所以单位时间内流量很小，但是公用有一个好处就是无限流量。

所以，官方推荐你自己建立私用 ID，这样每个私用 ID 给你 1G 的流量，每个人貌似可以有 12 个，私用 ID 每天都有1G的流量，当然，这个情况是很老的版本了，现在是什么情况，我也不知道了，毕竟，我觉得公用 ID 就够我用了。

但是，我在老版本遇到过这样一个情况。我还没开始用私用 ID 的流量就用完了。好几天都是这样，但是私用的用完后，我不会转公用的，导致不能科学上网，因为我的私用额度用完了，这个情况满普遍的，经过询问后，解决方案如下。

{% codeblock %}
结束进程 pythonw.exe，编辑 config.ini 或 manual.ini ，将如下内容的 appid 前添加;（注释以停用）
[gae]
;添加你自己的appid，多个用竖线 | 分隔
appid =xxx|yyy|zzz
;appid密码，无可不填
password =
config.ini 在data文件夹中
{% endcodeblock %}
