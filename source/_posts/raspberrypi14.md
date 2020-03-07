---
title: 树莓派 zero w 和 ADS1115 连接
date: 2019-01-08 18:41:33
categories:
- raspberry
tags:
- 树莓派
- zero w
- ADS1115
- 模数转换
- I2C
---
本来是打算连接 ADS1256 的，但是，怎么都不成功，所以，打算连接一下 ADS1115 来测试一下，树莓派是否能用。

<!--more-->

## 环境介绍

	raspberry zero w
	ADS1115
	system
		2018-11-13-raspbian-stretch-lite.img
	python2.7
		
## 参考资料

[书籍：爱上树莓派的附录：模拟信号输入](http://www.elecfans.com/soft/35/2018/20180506673029.html)

[Adafruit_Python_ADS1x15](https://github.com/adafruit/Adafruit_Python_ADS1x15)

[ADS1015 / ADS1115](https://learn.adafruit.com/raspberry-pi-analog-to-digital-converters/ads1015-slash-ads1115)

## 需要知道的知识

ADS1256 是靠 SPI 传递信息，而 ADS1115 是靠 I2C 来传递信息。

现在假设你已经配置好环境了，比如你已经可以用你的电脑或者其他方式可以连接你的树莓派了。

首先的第一步是开启 I2C 。

运行命令 

	sudo raspi-config
	
{% img /images/raspberry/14_0.png %}

{% img /images/raspberry/14_1.png %}

## 连接线

	ADS1x15 VDD to Raspberry Pi 3.3V
	ADS1x15 GND to Raspberry Pi GND
	ADS1x15 SCL to Raspberry Pi SCL
	ADS1x15 SDA to Raspberry Pi SDA

{% img /images/raspberry/14_2.png %}

## 配置运行环境

	sudo apt-get update
	sudo apt-get install i2c-tools python-smbus
	
然后我们运行下面的命令来看 ADS1115 是否被识别了

	sudo i2cdetect -y 1
	
{% img /images/raspberry/14_3.png %}

## 配置第三方库

说实话，第三方库的配置搞得我很烦躁，因为虽然我安装了 pip ，但是通过 pip 安装第三方库总是出现错误，即便是安装成功了，也是不能用，所以，在树莓派下我们用 sudo apt-get install *** 来安装。

运行下面的命令。

	sudo apt-get update
	sudo apt-get install build-essential python-dev python-smbus python-pip
	
上面的第二条命令我们有的第三方库可能会安装不成功。

所以，我们要单独安装 pip 之类的库。

如果运用下面的命令的话，我们也是失败的，可能是因为库太老了。

	sudo pip install adafruit-ads1x15
	
所以，我决定直接到这个库中下载。

[库地址](https://github.com/adafruit/Adafruit_Python_ADS1x15)

下载的格式为 zip ，然后，我们将其下载到目录下。

解压后，进入到相关目录，我们执行下面的命令，有一点我们需要明白的是，执行下面这一条命令必须得安装 pip 。

	sudo python setup.py install
	
成功后，执行 

	pip list
	
我们就会看见第三方库已经安装上去了。

{% img /images/raspberry/14_4.png %}

我们进入到相关目录下，就能看见里面有示例代码。

{% img /images/raspberry/14_5.png %}

示例代码就在 examples 中。

我们要知道，示例代码是用 python2.7 写的，所以执行

	sudo python simpletest.py
	
{% img /images/raspberry/14_6.png %}

即，程序正确执行。

## 需要了解的其他的事

我们上面所用的库是已经被废弃的，改进后的库的地址下面给出。

[Adafruit_CircuitPython_ADS1x15](https://github.com/adafruit/Adafruit_CircuitPython_ADS1x15)

### 系统版本

我们上面安装的系统只带命令行，所以，下载下来很多东西都是没有的，所以，我们安装的系统应该是带有图形化界面的，如果是这样的话，系统就自带 pip numpy pillow 等库，节省我们很大的工作量。

我们就可以直接在 github [库地址](https://github.com/adafruit/Adafruit_Python_ADS1x15) 中下载 zip 文件，解压后，进入文件里面，执行

	sudo python setup.py install
	
就可以安装了。












