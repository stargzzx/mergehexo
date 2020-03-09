---
title: 树莓派更换镜像源
date: 2018-12-29 11:36:27
categories:
- raspberry
tags:
- 树莓派
- raspberry
---
都是墙啊。。。

<!--more-->

这是一个非常重要的知识点（敲黑板）。众所周知，树莓派的服务器在国外，所以对于在国内的我们来说，下载或更新非常的缓慢（如果你有路由器级别的梯子的话，自然也可以略过）。

好在国内有很多大学或者机构把国外的服务器做了镜像拷贝，并且免费供我们使用（在这里非常感谢他们做出的贡献，谢谢你们，你们让世界变得美好了许多）。我们只需要把地址更换成国内的地址，就可以大大提升速度和稳定性。

首先我们使用Root权限nano编辑器打开绝对地址为/etc/apt/sources.list的文件。即在命令行中输入：

	sudo nano /etc/apt/sources.list
	
你会看见如下画面。

![](/images/raspberry/10_0.png)

deb开头的就是镜像站的地址，这个地址链接的是国外的服务器，我们要做的就是把它改掉。

你可以把这里面的信息直接全部删除。或者用另外一种方法——代码注释。

代码注释的意思就是在最前面输入＃号，那么这一行代码计算机就会无视，一般是程序员用于写一些标签之类的东西。一般来说不同的语言代码注释的方法也不同。使用代码注释的好处是之后如果需要，也可以随时复原。

那么我们就注释掉，如下。

![](/images/raspberry/10_1.png)

之后在下面粘贴国内镜像站的地址，如下。

![](/images/raspberry/10_2.png)

地址不一定要和上面演示的一致，你可以自由选择镜像站。我这里列出几个国内常见Raspbian OS镜像站及地址。

清华大学开源软件镜像站：

	deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ jessie main non-free contrib
	deb-src http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ jessie main non-free contrib
	
中国科学技术大学Linux用户协会：

	deb http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main non-free contrib
	deb-src http://mirrors.ustc.edu.cn/raspbian/raspbian/ jessie main non-free contrib
	
浙江大学开源镜像站：

	deb http://mirrors.zju.edu.cn/raspbian/raspbian/ jessie main contrib non-free rpi
	deb-src http://mirrors.zju.edu.cn/raspbian/raspbian/ jessie main contrib non-free rpi

华中科技大学开源镜像站：

	deb http://mirrors.hust.edu.cn/raspbian/raspbian/ wheezy main non-free contrib
	deb-src http://mirrors.hust.edu.cn/raspbian/raspbian/ wheezy main non-free contrib
	
阿里巴巴开源镜像站：

	deb http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib
	deb-src http://mirrors.aliyun.com/raspbian/raspbian/ wheezy main non-free contrib

更多的就不再罗列了，在选择镜像站的时候，还是选择地理位置接近的为宜。

更换后，Ctrl＋O回车保存，Ctrl＋X退出nano编辑器，之后在命令行输入（逐行输入，这是两条命令）

	sudo apt-get update
	sudo apt-get upgrade

sudo不再解释。

apt-get命令是APT软件包管理工具，所有基于Debian的Linux发行版都使用这个包管理系统。如果你听不懂我在说什么，就理解为这是app store就好了。第一句update是更新列表，第二句是更新upgrade是更新所有已安装的app。