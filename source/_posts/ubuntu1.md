---
title: ubuntu 更换镜像源
date: 2019-01-19 03:05:23
categories:
- ubuntu
tags:
- ubuntu
---
由于国内墙的原因，所以，我们要更换一下镜像源。
<!--more-->
## 参考资料

[ubuntu更改镜像源（软件源）](https://blog.csdn.net/weixin_41762173/article/details/79480832)

## 更新Ubuntu软件下载地址

### 寻找国内镜像源

所谓的镜像源：可以理解为提供下载软件的地方，比如Android手机上可以下载软件的91手机助手；iOS手机上可以下载软件的AppStore

这里我们的是清华得镜像源。

[清华镜像源地址](https://mirrors.tuna.tsinghua.edu.cn/)

{% img /images/ubuntu/1_0.png %}

我们一定要记住，要选用和自己版本匹配得镜像源。

{% img /images/ubuntu/1_1.png %}

{% img /images/ubuntu/1_2.png %}

### 备份Ubuntu默认的源地址

	sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup

{% img /images/ubuntu/1_3.png %}

### 更新源服务器列表

{% img /images/ubuntu/1_4.png %}

{% img /images/ubuntu/1_5.png %}

### 更新源

{% img /images/ubuntu/1_6.png %}

## Ubuntu软件操作的相关命令

{% codeblock %}
sudo apt-get update  更新源
sudo apt-get install package 安装包
sudo apt-get remove package 删除包
sudo apt-cache search package 搜索软件包
sudo apt-cache show package  获取包的相关信息，如说明、大小、版本等
sudo apt-get install package --reinstall   重新安装包
sudo apt-get -f install   修复安装
sudo apt-get remove package --purge 删除包，包括配置文件等
sudo apt-get build-dep package 安装相关的编译环境
sudo apt-get upgrade 更新已安装的包
sudo apt-get dist-upgrade 升级系统
sudo apt-cache depends package 了解使用该包依赖那些包
sudo apt-cache rdepends package 查看该包被哪些包依赖
sudo apt-get source package  下载该包的源代码
sudo apt-get clean && sudo apt-get autoclean 清理无用的包
sudo apt-get check 检查是否有损坏的依赖
{% endcodeblock %}








