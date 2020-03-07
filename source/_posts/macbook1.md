---
title: macbook安装MySQL
date: 2019-10-03 20:28:59
categories:
- MacBook
tags:
- MacBook
- system
- MySQL
- 数据库
---
这是在MacBook上安装MySQL。

<!-- more -->

<br/>

# 参考资料

<br/>

[Mac 安装 MySQL](https://blog.csdn.net/catstarxcode/article/details/78940385)

<br/>

# 安装

<br/>

登录MySQL网站

用dmg的方式安装。

{% img /images/macbook/1_0.png %}

数据库下载服务器就好了，不需要下载图形化界面。

选择dmg文件下载

{% img /images/macbook/1_1.jpg %}

选择选择不登录,直接下载

接着, 会跳转到如下页面, 你只需要选择不登录,直接下载即可

<br/>

# 记住临时密码

<br/>

下载完成后，双击打开一路确定，但是当弹出一个MYSQL Installer提示框的时候一定打开备忘录复制粘贴记下弹出框的密码

2016-10-16T02:52:44.474550Z 1[Note] A temporary password is generated for root@localhost: /kL_M_zXd3rA。

或者只复制密码部分就可以了/kL_M_zXd3rA为什么不截图记下来呢？因为密码有点复杂囧不好打。

ps：可能之前的教程有点老了，所以，需要记住临时密码，我的版本不需要记住，是我手动输入自己的密码的，但是，还是建议记录一下临时密码以备后患。

{% img /images/macbook/1_2.jpg %}

<br/>

# 打开MySQL服务

<br/>

正常情况下，安装成功。

此时只是安装成功，但还需要额外配置（我是2019年版本的，安装好后直接自动开启了服务，而且，下面的界面也变了，但是万变不理其宗，还是弄一下吧）：

另外，由于我的hexo是放在win下面的，在MacBook截图传过来有些麻烦，所以，直接用了网上的图片。

## 进入系统偏好设置

{% img /images/macbook/1_3.jpg %}

## 点击MySQL

{% img /images/macbook/1_4.jpg %}

## 开启MySQL服务

{% img /images/macbook/1_5.jpg %}

打开后界面上红色的部分会变成绿色的running。

{% img /images/macbook/1_6.jpg %}

<br/>

# 配置路径

<br/>

关于配置路径，你可以看看我的

[MacBook的环境变量](https://benpaodewoniu.github.io/2019/10/03/macbook0/)

我们的MySQL是安装在下面的路径上的

	/usr/local/mysql/bin

所以，我们要

	vim ~/.bash_profile

加入

	PATH=$PATH:/usr/local/mysql/bin
	
并保存（vim 中先按 Esc键，在输入  :wq  ）。







