---
title: 手机模拟器与 XPosed
date: 2019-03-11 15:09:46
categories:
- plug_in
tags:
- xposed
- XPosed
- 手机
- 手机模拟器
- android
- 模拟器
- 外挂
- 手机外挂
---
这个是进行云模拟真机的基础篇.
<!-- more -->
## 参考资料
[夜神模拟器4.4系统激活XPosed框架的教程](https://jingyan.baidu.com/article/fedf0737bafd7d35ac8977a0.html)
[夜神安卓模拟器怎么设置代理](https://blog.csdn.net/wudinaniya/article/details/78841564)
[夜神安卓模拟器怎样安装xposed框架](https://blog.csdn.net/xiangshangbashaonian/article/details/82386233)
[Xposed Module Repository](https://repo.xposed.info/module-overview)
[如何利用xposed框架模拟真实手机环境，建立引流脚本矩阵](https://zhuanlan.zhihu.com/p/56897537)
[如何查看和添加windows下的端口号](https://jingyan.baidu.com/article/f25ef254757b89482c1b82f2.html)
[中文 XPosed](https://xposed.appkg.com/category/modules)
[模拟器 安装xposed框架](https://jingyan.baidu.com/article/48206aead5bc52216ad6b3e9.html)
## 模拟器评测
我测试过三款模拟器,分别是雷神,夜神,mumu.
最后我选择的是夜神.
## 下载 XPosed
[下载地址](https://repo.xposed.info/module/de.robv.android.xposed.installer)
我们一定要注意 Android 的版本,一般的话都是 4.2-4.4 ,如果选择的安装版本不对应,是不能运行的.
而且, XPosed 我是在模拟器上运行的,没有在真机上运行.
## 安装 XPosed
将适配版本xposed模块安装器apk拖入安卓模拟器进行安装
在夜神模拟器上启动XPosed Installer，进入XPosed框架安装管理器。
{% img /images/plug_in/5_0.png %}
进入XPosed框架后点击“框架”进入框架界面。
{% img /images/plug_in/5_1.png %}
接下来点击“安装/更新”，如果弹出root授权界面，请点击允许root授权。
{% img /images/plug_in/5_2.png %}
在弹出来的重启设备对话框中，点击“是”进入模拟器系统重启程序。
{% img /images/plug_in/5_3.png %}
重启完成之后，再次进入XPosed Installer，点击“模块”，这一步之前请先安装好XPosed模块手机信息修改工具。
所谓的模块其实也就是 apk ,即应用,所以我们要事先下载好相关的应用.
[官方下载地址](https://repo.xposed.info/module-overview)
[中国XPosed模块下载地址](https://xposed.appkg.com/category/modules)
{% img /images/plug_in/5_4.png %}
在模块中将我们要测试的XPosed模块手机信息修改器打钩。
{% img /images/plug_in/5_5.png %}
再次进入“框架”，点击“软重启”或“重启”重启系统。
{% img /images/plug_in/5_6.png %}
重启之后，打开手机信息修改器模块工具程序，可以看到软件可正常使用。
{% img /images/plug_in/5_7.png %}
















