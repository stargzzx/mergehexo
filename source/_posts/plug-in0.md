---
title: 使用 Airtest 通过 USB 连接手机
date: 2019-03-11 06:48:21
categories:
- plug_in
tags:
- plug_in
- AirtestIDE
- 外挂
- 手机外挂
---
利用 python 的语言特性，进行控制手机，效果和人的操作一样。
<!-- more -->
## 参考资料
[全面超越Appium，使用Airtest超快速开发App爬虫](https://juejin.im/post/5c42fd6251882525153c325a)
## 用的手机机型
oppo R11
## 利用网易的 AirtestIDE 开发爬虫
首先要下载这个工具。
[下载地址](http://airtest.netease.com/)
想开发网页爬虫，发现被反爬了？想对 App 抓包，发现数据被加密了？不要担心，使用 Airtest 开发 App 爬虫，只要人眼能看到，你就能抓到，最快只需要2分钟，兼容 Unity3D、Cocos2dx-*、Android 原生 App、iOS App、Windows Mobile……。
Airtest是网易开发的手机UI界面自动化测试工具，它原本的目的是通过所见即所得，截图点击等等功能，简化手机App图形界面测试代码编写工作。
爬虫开发本着天下工具为我所用，能让我获取数据的工具都能用来开发爬虫这一信念，决定使用Airtest来开发手机App爬虫。
安装完之后如下图：
{% img /images/plug_in/0_0.png %}
### 连接手机
以Android手机为例，由于Airtest会通过adb命令安装两个辅助App到手机上，再用adb命令通过控制这两个辅助App进而控制手机。
因此首先需要确保手机的adb调试功能（也就是开发者模式，并且打开 USB 调试）是打开的，并允许通过adb命令安装App到手机上。
启动Airtest以后，把Android手机连接到电脑上，点击下图方框中的refresh ADB：
{% img /images/plug_in/0_1.png %}
此时在Airtest界面右上角应该能够看到手机的信息，如下图所示。
{% img /images/plug_in/0_2.png %}
点击connect按钮，此时可以在界面上看到手机的界面，并且当你手动操作手机屏幕时，Airtest中的手机画面实时更新。如下图所示。
{% img /images/plug_in/0_3.png %}
关于 Airtest 的界面介绍我就不介绍了，因为到后期我们是脱离这个 IDE 的，直接转向 pycharm 开发。
而且，在这里我也不介绍关于通过像素识别来操纵手机的方法了，我们直接进入下一个高级话题，通过 App 布局信息操作手机。


























