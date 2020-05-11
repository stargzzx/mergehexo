---
title: 电脑无线连接手机
date: 2019-03-11 13:07:35
categories:
- 脚本控制
- 手机
tags:
- 无线
- plug_in
- python
- airtest
- popc
- 外挂
- 手机外挂
- adb
- android
---
这个就比较厉害了.

<!-- more -->

## 参考资料

[在Airtest中如何使用无线模式控制手机](https://www.cnblogs.com/xieqiankun/p/wireless-mode-of-poco.html)
[如何配置android的adb环境变量](https://jingyan.baidu.com/article/17bd8e52f514d985ab2bb800.html)
[Android 网络调试 adb tcpip 开启方法](https://blog.csdn.net/shawnkong/article/details/8923933)
[解决adb调试报错error:device not found](https://jingyan.baidu.com/article/e75aca857b8560142edac6a0.html)
[关于adb shell命令后报错error:device not found的一种解决方案](https://blog.csdn.net/whurs/article/details/52857647)
[开启andriod手机的adbd，进行无线adb调试](https://blog.csdn.net/killfunst/article/details/45896911)
[error: device unauthorized.This adb server's $ADB_VENDOR_KEYS is not set 问题的解决](https://blog.csdn.net/wolfking0608/article/details/79468738)

## 配置 adb 环境变量

[android](https://developer.android.com/studio)

![](/images/plug_in/3_0.png)

下载下来之后,将有 adb.exe 目录添加到环境变量里面,此时我们的 adb 环境变量就配置好了.

## 连接方法

做法分为两种：第一种是在Airtest的IDE中控制手机。第二种是在Python代码里面控制远程手机。

### 启动开启手机上的adb端口

无论使用哪种方式，要远程控制手机，就需要首先把手机上的adb端口打开。这一步必需先用USB线把手机连上电脑。

在终端里面执行命令：

	adb tcpip 48887
	
其中的端口号48887你可以自行设定为其他的端口号，但不能和已有的端口冲突。

命令执行完成以后，你就可以拔下USB线了。接下来就是远程控制手机。

### 查明手机的 IP 地址

我们电脑和手机必须连接同一个局域网.

在浏览器上输入

	http://192.168.1.1/
	
进入界面后,找到自己要控制的手机 IP .

![](/images/plug_in/3_1.png)

### 在AirtestIDE中无线遥控手机

打开Airtest，点击下图红框框住的 remote connection:

![](/images/plug_in/3_2.png)

在弹出来的输入框中，输入：

	adb connect 手机IP:端口
	
这条命令可能会出错,错误的解决手段可以在参考链接中找到.

因为,我没有截下错误的图片,所以,就不写了.

有的时候你只需要重新拔插就好了.

其中手机的IP你可以在无线路由器中找到，也可以在手机的系统设置中找到。端口就是上一条命令设定的端口。

例如：

	adb connect 192.168.0.102:48887

如下图所示。

![](/images/plug_in/3_3.png)

点击 connect ，此时手机就会在上方的 Device列表中出现。双击它就可以无线连接手机并看到手机屏幕了。

### 在Python中控制手机

首先说明，Airtest的官方文档有问题，如果你跟着文档来写代码，一定会失败。

官方文档中，在

[连接安卓设备](https://airtest.readthedocs.io/zh_CN/latest/README_MORE.html#connect-android-device)

有一段介绍如何连接远程安卓手机的例子：

	本地设备
	# connect a remote device using custom params
	connect_device("android://adbhost:adbport/1234566?cap_method=javacap&touch_method=adb")

这个代码看起来，你可能会把Python代码写为：

	from airtest.core.api import *
	device_1 = connect_device('android://192.168.0.100:48887/手机串号?cap_method=javacap&touch_method=adb')
	
如果你这样写，你一定无法连上手机。

正确的代码为：

	from airtest.core.api import *
	device_1 = connect_device('android:///192.168.0.100:48887?cap_method=javacap&touch_method=adb')
	
只有按我这里的写法才能正确控制手机。如下面的gif所示。

![](/images/plug_in/3_4.gif)

动图中涉及到的完整代码为：

```python
from airtest.core.api import *
import time
from poco.drivers.android.uiautomation import AndroidUiautomationPoco
device_1 = connect_device('android:///192.168.0.100:48887?cap_method=javacap&touch_method=adb')
poco = AndroidUiautomationPoco(device_1, use_airtest_input=True, screenshot_each_action=False)
poco(text="知乎").click()
poco(name="com.zhihu.android:id/input").click()
poco(name="com.zhihu.android:id/input").set_text('古剑奇谭三')
time.sleep(2)
poco(name='com.zhihu.android:id/magi_title', textMatches='^古剑奇谭三.*$').click()
poco.swipe([0.5, 0.8], [0.5, 0.2])
```






























