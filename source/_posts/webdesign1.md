---
title: 本地构建wordpress
date: 2019-07-08 14:23:13
categories:
- 前端设计
- wordpress
tags:
- webdesign
- wordpress
- 网站
---
因为公众号的业务现在太广了，单一的公众号已经不能满足业务需求了，所以，我们必须得要一个网站来进行分化业务。

<!-- more -->

经过考量，我最终选择wordpress + knowhow（主题）。

# 本地安装环境

wordpress需要apache+mysql。所以，我选择轻量级的集合环境——xampp。

## 安装xampp

这个就从网上下载下来就行了，傻瓜式安装。

![](/images/webdesign/1_1.png)

首先点击里面的 xampp_start.exe。

然后在目录下有一个 xampp-control.exe ，点击启用它。

![](/images/webdesign/1_0.png)

然后我们启用 apache 和 mysql ，当他们变绿就好了，但是，我在启用的时候，发现 mysql 并没有变绿。

但是，在下面有一个话是 XAMPP MySQL is already running on port 3306。

所以，证明我的 mysql 已经OK了。

## 创建数据库

用浏览器登录下面的链接。

[链接](http://127.0.0.1/phpmyadmin/)

我们需要创建一个database

![](/images/webdesign/1_2.png)

## 创建 wordpress

将WordPress程序放到相应位置：解压缩wordpress程序，将文件命名为wordpress,放置到安装的XAMPP(Phpnow)文件夹下的htdocs文件里。

![](/images/webdesign/1_3.png)

本地安装wordpress程序：浏览器中输入：http://127.0.0.1/wordpress，

按照提示步骤安装程序即可，截图如下：

![](/images/webdesign/1_4.png)

![](/images/webdesign/1_5.png)

这里需要特别注意的是，那个数据库的名字，就是你之前创建的数据库的名字。

# 后续

到这里wordpress就已经安装好了，其他的就可以配置了，我使用的是 knowhow 主题，在后面我就讲一下这个主题的坑。













