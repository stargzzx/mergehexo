---
title: 树莓派的配置 boot/config.txt
date: 2018-12-28 11:08:12
categories:
- raspberry
tags:
- 树莓派
- raspberry
---
我用的是微雪 7 寸屏幕( 7inch HDMI LCD (C) )。板子是 zero w 的，ok，开始我们愉快的配置之旅吧。
<!--more-->
配置文件实在是太复杂了，还是等用的时候再说吧。另外，附上两个链接，以后供完善这篇 blog 用。
[树莓派配置文档config.txt说明](https://my.oschina.net/funnky/blog/132885)
[树莓派配置文件config.txt详细介绍](https://www.jianshu.com/p/4b6934487587)
## 通解
[树莓派配置文件config.txt详细介绍](https://www.jianshu.com/p/4b6934487587)
### config.txt文件介绍
由于树莓派并没有传统意义上的BIOS, 所以现在各种系统配置参数通常被存在”config.txt”这个文本文件中，树莓派的config.txt文件会在ARM内核初始化之前被GPU读取。这个文件存在引导分区上的，对于Linux, 路径通常是/boot/config.txt, 如果是Windows (或者OS X) 它会被识别为SD卡中可访问部分的一个普通文件.
将树莓派SD卡通过读卡器接到电脑上，就可以修改这个文件。
也可以 SSH 连接之后，在命令行中进行修改，这种修改方式更加装逼，对吧，嘿嘿嘿。
### 文件格式
当值是整形时格式为"属性=值". 每行只指定一个参数. 注释使用'#'井号作为一行开头.
注意: 在新版的树莓派里每行都有#注释, 要想使用该行参数只需移除#.
并且等号两边是没有空格的。
## 显示器配置
[官方配置](http://www.waveshare.net/wiki/7inch_HDMI_LCD_(C))
你可以在上面链接中找到你自己的配置文件。
注意：有些用户由于在一开始在连接树莓派使用的时候没有设置分辨率，重新设置分辨率并重启后出现闪屏和残影现象。这种情况下将屏幕断电放置几个小时后重新上电即可。
### 产品特点

	1024×600超清分辨率
	电容式触摸控制
	支持Raspberry Pi
	支持Banana Pi、Banana Pro，提供Lubuntu，Raspbian相应镜像
	支持BB Black，提供Angstrom相应镜像
	通用HDMI显示屏，可作为计算机显示器
	HDMI接口用于显示，USB接口用于触摸
	支持背光控制，更省电
	
### 配置文件
编辑TF卡根目录下的config.txt文件。在config.txt文件末尾添加：

	max_usb_current=1
	hdmi_group=2
	hdmi_mode=87
	hdmi_cvt 1024 600 60 6 0 0 0
	hdmi_drive=1
	
必须确保等号两边没有空格。
### 旋转
在config.txt文件中加入语句（config文件位于TF卡根目录，即/boot中）：

	display_rotate=1
	
保存后重启即可。
注： 虽然我配置了旋转操作，但是，并没有什么卵用，但是，关于旋转操作还需要其他的组建，由于没有这方面的需求，我就没有再继续下去，所以，如果真的需要旋转，你可以去上面的链接上看看。
