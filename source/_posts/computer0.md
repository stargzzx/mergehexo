---
title: Win10更新后卡顿怎么办，2018win10爆卡的解决办法
date: 2018-10-28 19:57:12
categories:
- computer
tags:
- win10
- computer
- skill
---
在 2018 年 10 月 28 日，万恶的 win10 自动更新了，再经过艰难的等待后，电脑终于能用了。

结果是爆卡。。。

原文章链接。

[Win10更新后卡顿怎么办，2018win10爆卡的解决办法](http://www.xiazaiba.com/jiaocheng/45248.html)

<!-- more -->

出现问题的电脑大多是升级到Win10四月版更新后，这和新版里一项新增加的“时间线”（Time Line）功能有着很大关系。

那么能否将这项功能关闭，让电脑重新恢复神力呢？

“时间线”按钮位于任务栏左侧，紧临小娜Cortana，点击后系统会自动给出近一段时间用户打开或访问过的文件及网页历史，帮助使用者快速回溯几天甚至几十天前的的操作。

但这项功能同时也是非常消耗系统资源的，而且需要Windows搜索服务（Windows Search）的辅助配合，那么该如何关闭它呢？

## 关掉鸡肋功能

设置面板

点击“开始”→“设置”→“隐私”→“活动历史记录”，取消“允许Windows从此电脑中收集我的活动”、“允许Windows将我的活动从此电脑同步到云”、“这些是你在此电脑上拥有的账户。

关闭它们，可将其活动从你的时间线中隐藏。”三组选项前面的复选框，这是关闭模块的第一步。

![](/images/computer/1_0.jpg)

注册表

接下来还要关闭注册表里的相关模块，具体操作如下：点击任务栏Cortana搜索框，输入“regedit”启动注册表编辑器，查找并定位到“计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\System”，

然后在右侧窗格中右击并新建一个32位DWORD值，命名为“EnableActivityFeed”，接下来将键值赋为“0”。

重启或注销当前账户后，整个时间线（Time Line）功能便被彻底关闭了。现在再来试一试吧，看你的电脑是不是又和以前一样轻快如初了！

![](/images/computer/1_1.jpg)

通过注册表彻底关闭Time Line模块

写在最后

除此之外，之前的一些Win10优化技巧也依旧有效，比如关闭“Connected User Experiences and Telemetry”、“Diagnostic System Host”、“Diagnostic Service Host”、“Diagnostic Execution Service”服务等等，都可以很快让电脑速度提升一个等级。

以上就是Win10更新后卡顿，电脑win10系统爆卡的解决办法，主要是关闭时间线这个新功能，希望微软官方能针对这个功能优化一下，不然大家电脑卡到爆，新功能就没有谁用了。

