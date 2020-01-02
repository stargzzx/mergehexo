---
title: 树莓派的针脚对应的编程语言
date: 2019-01-08 20:17:34
categories:
- raspberry
tags:
- 树莓派
- GPIO
---
我们可以用 C 和 Python 来控制树莓派的针脚。
但是，这两个不同的针脚其实各自需要不同的库。
这个 BLOG 除了会介绍上面的内容外，还会放一下对应的针脚图。
<!--more-->
[树莓派GPIO控制--C语言篇](https://blog.csdn.net/hu7850/article/details/51785560)
树莓派内核中已经编译自带了gpio的驱动，我们常通过一些第三方写好的库函数来完成具体的操作，比较常见的操作库函数有：
# 常见的函数库
## Python GPIO
>【开发语言】——python
【简单介绍】——树莓派官方资料中推荐且容易上手。python GPIO是一个小型的python库，可以帮助用户完成raspberry相关IO口操作，但是python GPIO库还没有支持SPI. I2C或者1-wire等总线接口。
【官方网站】—— https://code.google.com/p/raspberry-gpio-python/

## wiringPi
>【开发语言】——C语言
【简单介绍】——wiringPi适合那些具有C语言基础，在接触树莓派之前已经接触过单片机或者嵌入式开发的人群。wiringPi的API函数和arduino非常相似，这也使得它广受欢迎。作者给出了大量的说明和示例代码，这些示例代码也包括UART设备，I2C设备和SPI设备等。
【官方网站】—— http://wiringpi.com/

## BCM2835 C Library
>【开发语言】——C语言
【简单介绍】BCM2835 C Library可以理解为使用C语言实现的相关底层驱动，BCM2835 C Library的驱动库包括GPIO. SPI和UART等，可以通过学习BCM2835 C Library熟悉BCM2835相关的寄存器操作。如果有机会开发树莓派上的linux驱动，或自主开发python或PHP扩展驱动，可以从BCM2835 C Library找到不少的“灵感”。
【官方网站】—— http://www.airspayce.com/mikem/bcm2835/

# 树莓派GPIO编号方式
## 功能物理引脚

	从左到右，从上到下：左边基数，右边偶数：1-40

## BCM

	编号侧重CPU寄存器，根据BCM2835的GPIO寄存器编号。
	
## wiringpi

	编号侧重实现逻辑，把扩展GPIO端口从0开始编号，这种编号方便编程。
	
# 样例图
{% img /images/raspberry/15_0.jpg %}
{% img /images/raspberry/15_1.png %}
# 在命令行中直接输出针脚图
要输出针脚图，我们必须得安装 wiringPi
## 安装方式 wiringPi
### 使用 GIT

	git clone git://git.drogon.net/wiringPi
	cd wiringPi
	./build
	build脚本会帮助你编译和安装wiringPi

### 直接下载

	在https://git.drogon.net/?p=wiringPi;a=summary下载最新版本编译使用
	tar xfz wiringPi-xx.tar.gz
	cd wiringPi-xx
	./build

### 树莓派中使用 apt-get 安装

	sudo apt-get install wiringpi
	
### 测试

	wiringPi包括一套gpio命令，使用gpio命令可以控制树莓派上的各种接口，通过以下指令可以测试wiringPi是否安装成功。
	$gpio -v

### 输出
在命令行中输入
	
	gpio readall
	
就会出现下面的图。
{% img /images/raspberry/15_2.png %}
### 样例代码
{% codeblock %}

#include <wiringPi.h> 
int main(void) 
{ 
	wiringPiSetup() ; 
	pinMode (0, OUTPUT) ; 
	for(;;) 
	{ 
		digitalWrite(0, HIGH) ; delay (500) ; 
		digitalWrite(0, LOW) ; delay (500) ; 
	} 
}

{% endcodeblock %}
### 编译运行

	gcc -Wall -o test test.c -lwiringPi 
	sudo ./test

## BCM2835 C Library
我们要下载最新版，下面只是个示例，我们可以直接在官网上下载，然后将压缩包转到树莓派中。
### 下载

	wget http://www.airspayce.com/mikem/bcm2835/bcm2835-1.35.tar.gz
	
### 解压缩

	tar xvzf bcm2835-1.35.tar.gz
	
### 进入压缩目录

	cd bcm2835-1.35
	
### 配置编译

	./configuremake
	
### 执行检察

	sudo make check
	
### 安装bcm2835库

	sudo make install
	
### 样例代码
{% codeblock %}

#include < bcm2835.h> 
//P1插座第11脚 
#define PIN RPI_GPIO_P1_11 
int main(int argc, char **argv) 
{ 
	if (!bcm2835_init()) 
		return 1;

	// 输出方式 
	bcm2835_gpio_fsel(PIN, BCM2835_GPIO_FSEL_OUTP);

	while (1) 
	{ 
		bcm2835_gpio_write(PIN, HIGH); 
		bcm2835_delay(100);

		bcm2835_gpio_write(PIN, LOW); 
		bcm2835_delay(100); 
	} 
	bcm2835_close(); 
	return 0; 
}

{% endcodeblock %}
### 编译运行

	编译运行
	gcc -o blink blink.c -lbcm2835
	./blink
	注：
	1)IO的编号方式略有不同，采用BCM编码方式
	2)-lbcm2835表示动态加载bcm2835共享库


## python GPIO
可以看我的另一篇 BLOG
[树莓派的 hello world](https://benpaodewoniu.github.io/2018/12/27/raspberrypi6/)







