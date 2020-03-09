---
title: 模拟器连接
date: 2019-03-11 15:16:47
categories:
- plug_in
tags:
- xposed
- XPosed
- 手机
- 手机模拟器
- airtest
- popc
- android
- 模拟器
- 外挂
- 手机外挂
---
这个是重点中的重点.

<!-- more -->

## 参考资料

[如何利用xposed框架模拟真实手机环境，建立引流脚本矩阵](https://zhuanlan.zhihu.com/p/56897537)
[airtest之模拟器连接](https://www.cnblogs.com/xuanjian-91/p/10348749.html)
[代码里adb connect后，airtest断开连接](https://blog.csdn.net/weixin_43966315/article/details/84838659)
[夜神模拟器开启开发者选项](https://www.cnblogs.com/Sophia-Sophia/p/6100608.html)
[Android模拟器连接](http://airtest.netease.com/docs/docs_AirtestIDE-zh_CN/2_device_connection/3_emulator_connection.html)
[如何使用AirtestIDE连接安卓模拟器](https://jingyan.baidu.com/article/5bbb5a1bcef5a713eba179b6.html)
[手机修改设备信息之安装xposed框架](https://blog.csdn.net/magicbaby810/article/details/80083131)
[通过ADB命令获取当前activity](https://blog.csdn.net/youxiansanren/article/details/44220419)
[删除 adb devices emulator-5554 device](https://blog.csdn.net/jiangcsc/article/details/65441563)
[adb 查看并杀死进程](https://blog.csdn.net/shawnxiafei/article/details/80757439)
[使用python+poco+夜神模拟器进行自动化测试](https://blog.csdn.net/saint_228/article/details/84889017)
[python自动化--控制手机](https://www.jianshu.com/p/5e793da39813)
[使用python实现对安卓模拟器的自动化操作QQ！](https://blog.csdn.net/qq_41841569/article/details/86064239)

## 打开夜神的开发者模式

打开夜神模拟器，进入屏幕上的[设置]，如图

![](/images/plug_in/6_0.png)

划到[设置]的最后，进入[关于平板电脑]，如图

![](/images/plug_in/6_1.png)

多次点击[版本号]

![](/images/plug_in/6_2.png)

退回到[设置]目录，会看到[开发者选项]，如图

![](/images/plug_in/6_3.png)

## 使用 AirtestIDE 连接模拟器

当我们打开多个模拟器的时候,可以在 cmd 的窗口界面,输入

	adb devices
	
有时候这个命令会杀死设备,所以我们有时候用重新启动模拟器,然后用这条命令查.

我开了两个手机模拟器,所以截图如下.

![](/images/plug_in/6_4.png)

我们在连接界面分别输入 cmd 出现的两个地址.

![](/images/plug_in/6_5.png)

点击 连接.

在设备连接窗口会出现设备,我们点击那个三角,选择 Use Javacap 如果不选择的话可能会出现黑屏.

	use javacap:解决IDE中手机画面黑屏的问题
    use ADB orientation ：解决屏幕旋转问题
    use ADB touch：解决无法点击的问题
    在部分版本的模拟器上，可能勾选了 use javacap 后依然连接失败，此时可以尝试再勾选 use adb orientation 选项后再重新连接即可。
    雷电仅需勾选use adb orientation

然后点击 connect .

就会出现连接画面.

![](/images/plug_in/6_6.png)

## 使用 python 代码连接模拟器

首先打开模拟器的开发者模式.

按照之前的教程安装完所需的库.

还要安装 uiautomator2，pillow 这两个模块

因为我使用的是 anaconda 的虚拟环境,所以直接采用命令

	pip install --upgrade --pre uiautomator2
	pip install pillow
	
安装好依赖模块，还要在本地模拟器的adb做关联文件的安装，用

	python -m uiautomator2 init
	
测试已连接的所有设备。

上面的那个执行的时候,会在模拟器上安装 apk 

初始化这时候会自动安装这几个文件，因为有些文件是要连接github会出现不稳定的情况，多尝试几次就好，出现图片的界面，就说明初始化成功了。

所谓的图片说明,也就是模拟器会自动打开 github 的 uiautomator2 页面.

还是用 adb devices 查出设备,IP,端口信息 

我的其中一段代码如下:

{% codeblock %}
# -*- encoding=utf8 -*-
import time
__author__ = "Administrator"
import os
import random
from airtest.core.api import *

# auto_setup(__file__)

from poco.drivers.android.uiautomation import AndroidUiautomationPoco
device_1 = connect_device('android:///127.0.0.1:62025?cap_method=javacap&touch_method=adb')
poco = AndroidUiautomationPoco(device_1,use_airtest_input=True, screenshot_each_action=False)
poco(text="网易云音乐").click()
time.sleep(2)
poco.swipe([0.5,0.8],[0.5,0.2])
poco(name="com.netease.cloudmusic:id/bbz").click()
time.sleep(4)
poco(name="com.netease.cloudmusic:id/bbz").click()
poco(name="搜索").click()
poco(name="com.netease.cloudmusic:id/search_src_text").click()
poco(name="com.netease.cloudmusic:id/search_src_text").set_text("李香兰")
poco(name="收起").click()
poco.swipe([0.5,0.2],[0.5,0.8])
poco(name="com.netease.cloudmusic:id/ad2").click()
time.sleep(2)
poco(name="转到上一层级").click()
poco(name="android.support.v7.app.ActionBar$Tab").click()
poco(text="最近播放").click()
{% endcodeblock %}









