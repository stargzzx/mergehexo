---
title: 树莓派的 hello world
date: 2018-12-27 21:35:04
categories:
- raspberry
tags:
- 树莓派
- raspberry
---
这个 blog 是将树莓派的 LED 灯点亮。
<!--more-->
当我们拿到树莓派的时候，如果要用 GPIO ,我们应该先将 GPIO 的引脚进行焊接。
## 电路图
下面是我收集的电路图
{% img /images/raspberry/6_2.png %}
{% img /images/raspberry/6_1.jpg %}
{% img /images/raspberry/6_0.jpg %}
拿走不谢
## 连接方式
{% img /images/raspberry/6_4.jpg %}
{% img /images/raspberry/6_3.jpg %}
### 代码
使用 nano 或者 vim （命令行）文本编辑工具来编写 python 脚本程序。你也可以使用GUI界面的编辑软件，但是我推荐你使用命令行界面里可以使用的编辑工具。 
为什么？ 因为如果你在你的笔记本电脑或者台式电脑上，使用像 xShell 或者 SectCRT这样的软件来登录树莓派终端时，树莓派里面的 GUI界面的软件 都是不能使用的。你只能使用 命令行里的工具。所以，我们最好适应 命令行里的文本编辑工具（就是 nano 或者 vim 文本编辑工具）。
使用 nano （命令行）文本编辑工具，新建一个python 脚本文件 test.py
{% codeblock %}

import RPi.GPIO as GPIO
import time

GPIO.setmode(GPIO.BCM)
GPIO.setup(25, GPIO.OUT)

while True:
    GPIO.output(25, GPIO.HIGH)
    time.sleep(0.5)
    GPIO.output(25, GPIO.LOW)
    time.sleep(0.5)

{% endcodeblock %}
按 Ctrl + O 保存文件，然后按 Enter回车键 退出 nano工具。
执行下面的命令，来给 test.py 脚本文件加上 可执行 权限：
	
	sudo chmod +x test.py
	
运行代码

	python test.py
	
注：有时候我们可能会得到警告，但是，程序还是可以正常运行。

	blinkled.py:5: RuntimeWarning: This channel is already in use, continuing anyway.  Use GPIO.setwarnings(False) to disable warnings.GPIO.setup(25, GPIO.OUT)

## 代码解读
### 设置 GPIO 的命名规则

	GPIO.setmode(GPIO.BCM)
	
树莓派的GPIO的命名方式一共有两种	

	RPi.GPIO.BCM	以CPU对应的引脚的索引号(下图中的GPIOxx) 命名树莓派的引脚
	RPi.GPIO.BOARD	以树莓派板子上引脚的索引号（下图中的Pin No.） 命名树莓派的引脚
	
{% img /images/raspberry/6_0.jpg %}
### 设置使用的GPIO引脚 为 输出模式

	GPIO.setup(25, GPIO.OUT)
	
点亮 LED，当你执行完下面这条命令后， LED 就会被点亮

	GPIO.output(25, GPIO.HIGH)
	 
熄灭 LED

	GPIO.output(25, GPIO.LOW)
	
	
	
	
	