---
title: 更改键盘布局
date: 2018-12-26 09:28:05
categories:
- raspberry
tags:
- 树莓派
- raspberry
---
更改键盘布局的目的是让很多字符打出来。

<!--more-->

从前面我们也知道，树莓派是英国的一个公司开发的，但，平时我们的键盘使用的是美国标准，所以，导致很多字符在树莓派上打不出来，比如 | 。

所以，我们要更改布局，使其变成美国标准键盘。

先来普及一下键盘标准。

## PC104标准键盘

所谓PC104标准键盘是指键盘含有104个按键，国内和美国多使用该类型键盘，而欧洲大陆多使用PC105键盘，除了比PC104键盘多了一个按键之外，很多符号的位置也不相同。PC104键盘的外观如下图所示。

{% img /images/raspberry/3_0.jpg %}

## 配置流程

进入命令行界面，输入

	sudo dpkg-reconfigure keyboard-configuration
	
选则通用 104 键 pc 键盘

{% img /images/raspberry/3_1.png %}

按tab键，到OK，按Enter确定。

在键盘layout选择中，选Other。

{% img /images/raspberry/3_2.PNG %}

按tab键，到OK，按Enter确定.

然后在选项中，选English(US)

{% img /images/raspberry/3_3.PNG %}

按tab键，到OK，按Enter确定

再选English(US, alternative international)

{% img /images/raspberry/3_4.PNG %}

选择键盘默认布局

{% img /images/raspberry/3_5.png %}

compose key设置（从来没有见过这个按键）

{% img /images/raspberry/3_6.png %}

ctrl+alt+backspace组合键，类似于windows的ctrl+alt+delete。

{% img /images/raspberry/3_7.png %}

按tab键，到OK，按Enter确定

然后一路按OK

退出后，重启系统

sudo reboot



