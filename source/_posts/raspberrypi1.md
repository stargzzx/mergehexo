---
title: 烧录系统
date: 2018-12-26 09:27:47
categories:
- raspberry
tags:
- 树莓派
- raspberry
---
就是给树莓派装上系统。

<!--more-->

我们从网上购买的树莓派是一个裸板，所以，我们需要给树莓派装上系统才能运行。在此之前，我们首先要确保我们有如下的设备。

## 设备

树莓派的板子

一个至少 8G 的 TF 卡或者 SD 卡

[TF卡和SD卡的区别](https://benpaodewoniu.github.io/2018/12/28/hardware0/)

一个读卡器

## 烧录流程

我们要先下载烧录软件。

[win32diskimager](https://sourceforge.net/projects/win32diskimager/)

下载要烧录的系统。

我们有很多系统可供选择，但是我们优先选择 Raspbian OS。

下载地址如下:

[Raspbian](https://www.raspberrypi.org/downloads/raspbian/)

![](/images/raspberry/1_0.png)

这一共有三个版本，第一个是包含全部功能，包括一些软件，比如游戏，第二个是带有命令行和图形化界面，缺少很多软件，第三个是之带有命令行。

选择你需要的版本自己下载。
我们通常是点击 Download Torrent ，这样是数据源是很多服务器上的，所以下载的很快。

推荐用迅雷。

我们下载的是 img 文件，也就是镜像文件，但是，直接解压到 SD 卡是不能运行的，所以，我们必须一位位的写入 SD 卡，这个流程又叫烧录。

如果我们这个 SD 卡之前没用过，那么直接烧录就可以了，如果之前用过我们还得格式化，再者，如果你的 SD 卡分了两个包含两个的区，那么烧录软件在烧录的过程中会出错 error 5.后面我会讲解如何处理这个错误。

假设你的 SD 卡是正常的，我们打开烧录软件，填写好路径就可以，烧录成功后，点击 exit 。

如下图：

![](/images/raspberry/1_1.jpg)

如果烧录成功，我们用电脑打开（电脑安装的是 windows 系统）。我们会发现 SD 卡已经被分为两个区了，一个是 boot，另一个是不知名盘。

在 windows 下我们只能看见 boot 文件夹，其它是隐藏起来的，至此，烧录结束。

### 烧录中出现的错误

error 5

当你重新烧录的时候，你将 boot 格式化，又将上面所说的另一个分区格式化，这个时候就会出现两个分区，烧录软件并不能区分烧录到那个分区，所以，出错。

这里有很多种方法，我用的是方法二。

方法一

使用SD memory card formatter格式化SD卡，然后再通过Win32工具将镜像文件写入

下载SD memory card formatter软件

[软件地址](https://www.sdcard.org/downloads/formatter_4/)

安装SD memory card formatter软件并打开配置

选择你SD卡所在磁盘（一般插上卡时会主动选择）

选择Overwrite format（这点很重要！）

Volume label可以自由起名

![](/images/raspberry/1_2.png)

点击“Format”开始格式化，一般要10分钟

格式化完成后，你会发现你的磁盘完全清空了，没有任何文件

这个方法我没试过，但是看起来不是很适合多个分区的操作。

方法二

[原文链接](http://mattlinotes.blogspot.com/2017/03/sdraspberry-pi.html)

開啟[命令提示字元]

於命令列下輸入 ‘diskpart’，便會出現另一個視窗並出現提示列  ‘DISKPART>’

在DISKPART視窗中輸入‘list disk’.

會出現類似於下圖的說明

![](/images/raspberry/1_3.png)

我們8GB的SD卡目前是位於DISK 1，所以輸入

	select disk 1
	
查看SD卡被切割成幾個區域

	list partition
	
現在要刪除第一區

	select partition 1
	delete partition
	
最後

	delete partition override
	exit
	
我用的这个方法最后成功了。

## 除了 Raspbian OS 还有哪些 OS 可以选择

Raspbian OS 其实并不是官方出品的，它是基于 Debian 的 ARM 定制版本，只不过是受到官方主力推荐的 OS 罢了，还有很多 Linux 的发行版本支持树莓派，下面列出一些：

Raspbian Jessie With PIXEL：树莓派官方推荐系统，基于 Debain 8，带 PIXEL 图形界面。特点是兼容性和性能优秀。

Raspbian Jessie Lite：树莓派官方推荐系统，基于 Debain 8，不带图形界面。特点是兼容性和性能优秀，比 PIXEL 版本的安装包更小。

Ubuntu MATE：Ubuntu MATE 是针对树莓派的版本，界面个性美观。

Snappy Ubuntu Core：Ubuntu 针对物联网（IoT）的一个发行版本。支持树莓派。

CentOS：CentOS 针对 ARM 的发行版。支持树莓派。

Windows IoT：微软官方针对物联网（IoT）的一个 Windows 版本。支持树莓派。

FreeBSD：FreeBSD 针对树莓派的发行版。

Kali：Kali 针对树莓派的发行版，黑客的最爱。

Pidora：在 Fedora Remix 基础上针对树莓派优化过的操作系统。

