---
title: ubuntu 镜像进阶篇
date: 2019-11-30 10:52:51
categories:
- ubuntu
tags:
- ubuntu
---
你可以先看看我之前关于 ubuntu 的镜像博客。

[ubuntu 更换镜像源](https://benpaodewoniu.github.io/2019/01/19/ubuntu1/)

<!--more-->

对于Ubuntu系统， 不同的版本的源都不一样，每一个版本都有自己专属的源。 而对于 Ubuntu 的同一个发行版本，它的源又分布在全球范围内的服务器上。Ubuntu 默认使用的官方源的服务器在欧洲，从国内访问速度很慢。国内的阿里、网易以及一些重点高校也都有 Ubuntu 的源，所以在装完 Ubuntu 系统后最好把官方源更换为国内的源。

这里我将告诉大家如何更换为国内的源：

# 首先看看国内有哪些源

点击下方链接查看

[模板:18.04source](https://wiki.ubuntu.org.cn/%E6%A8%A1%E6%9D%BF:18.04source)

我们选择阿里云源与清华大学源（其他源都行），将它们的 Ubuntu 源的服务器地址先复制下来，下面会用到。

	阿里云源: 
		http://mirrors.aliyun.com/ubuntu/
	清华源: 
		http://mirrors.tuna.tsinghua.edu.cn/ubuntu/

# 获取 Ubuntu 代号

Ubuntu 每个发行版本都有自己的代号，我们要通过我们电脑上 Ubuntu 的代号去找对应的源，Ctrl+Alt+T 打开终端，执行以下命令：

	lsb_release -a

然后会得到我们自己的 Ubuntu 的版本信息 ，最后一栏 codename 后面的就是我们自己的 Ubuntu 的代号。比如我安装的是 Ubuntu 18.04.1，查出来的代号就是 bionic.

# 编辑源

嫌麻烦的同学可以直接观看方法2

## 方法 1 手动编写

打开上面的那个 模板:18.04 的链接

在 step 1 打开的那个网页最顶端找到 模板:18.04source ，将里面的内容复制到文本编辑器，

{% img /images/ubuntu/2_0.png %}


将上图中红色的边框内的服务器地址改为中阿里云源与清华大学源的服务器地址，然后将紫色边框中的内容改为你自己的Ubuntu 的代号。

例如：我的Ubuntu 18.04.1 代号是 bionic，所以更改后结果如下；

我个人不建议你直接复制

{% codeblock %}
# 阿里云源
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
##測試版源
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
# 源碼
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
##測試版源
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse


# 清华大学源
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
##測試版源
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
# 源碼
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-backports main restricted universe multiverse
##測試版源
deb-src http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ bionic-proposed main restricted universe multiverse
{% endcodeblock %}

## 方法2 直接复制

你可以先看看我前面的博客，来获得如何复制清华源的路径信息。

下面这个是阿里源的镜像信息链接。

[Ubuntu 镜像](https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.53322f705KX5sL)

# 修改源文件 sources.list

Ubuntu 的源存放在在 /etc/apt/ 目录下的 sources.list 文件中，修改前我们先做个备份，在终端中执行以下命令：

	sudo cp /etc/apt/sources.list /etc/apt/sources.list.bcakup

然后执行下面的命令打开 sources.list 文件，清空里面的内容，把上面我们编辑好的国内的源复制进去，保存后退出。

	sudo gedit /etc/apt/sources.list

# 更新软件列表和升级

在终端上执行以下命令更新软件列表，检测出可以更新的软件：

	sudo apt-get update

在终端上执行以下命令进行软件更新

	sudo apt-get upgrade