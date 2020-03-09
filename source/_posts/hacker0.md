---
title: DVWA
date: 2018-12-29 15:46:56
categories:
- hacker
tags:
- hacker
- DVWA
---
一个让你练习黑客技术的好平台。

<!-- more -->

## DVWA是什么

DVWA（Damn Vulnerable Web Application）是一个用来进行安全脆弱性鉴定的PHP/MySQL Web应用，旨在为安全专业人员测试自己的专业技能和工具提供合法的环境，帮助web开发者更好的理解web应用安全防范的过程。

即，它是一个平台，你可以下载到本地，然后进行你的黑客训练。

DVWA共有十个模块:

	1.Brute Force（暴力（破解））
	2.Command Injection（命令行注入）
	3.CSRF（跨站请求伪造）
	4.File Inclusion（文件包含）
	5.File Upload（文件上传）
	6.Insecure CAPTCHA （不安全的验证码）
	7.SQL Injection（SQL注入）
	8.SQL Injection（Blind）（SQL盲注）
	9.XSS（Reflected）（反射型跨站脚本）
	10.XSS（Stored）（存储型跨站脚本）
	
酷不酷？

## 所需软件

因为它需要 PHP MySQL ，所以，你得下载一个集成环境，这里介绍 xmapp。轻量简单易用。

[下载地址](https://www.apachefriends.org/zh_cn/index.html)

我们当然还的下载 DVWA 了。

[下载地址](http://www.dvwa.co.uk/)

下载下来是一个 zip 文件。

## xmapp 的使用

安装完成后点击运行

![](/images/hacker/0_0.jpg)

在Apache及MySQL选择行中点击start便可创建虚拟环境

![](/images/hacker/0_1.jpg)

测试

打开浏览器输入localhost测试是否搭建成功，显示该页面则搭建完成

![](/images/hacker/0_2.jpg)

## DVWA搭建

将下载的 zip 文件解压至XAMPP目录下的htdocs文件夹中，打开dvwa/config，修改 config.inc.php.bak（这里也可能不是 bak 但是都修改成右边的名字） 为 config.inc.php ，随后打开浏览器输入localhost/dvwa/setup.php （文件名不区分大小写）打开设置页面，并点击页面下方 Create/Reset Database 

![](/images/hacker/0_3.jpg)

### 可能的报错

	Could not connect to the database - please check the config file
	
原因是：$_DVWA[‘db_password’]=‘p@ssword’;这个密码必须和MySQL root用户的密码一致。

![](/images/hacker/0_4.jpg)

解决：打开dvwa目录下的config，打开config.inc.php，在图中第21行的

	$_DVWA[ 'db_password' ] = 'p@ssword';
	改为
	$_DVWA[ 'db_password' ] = '';

![](/images/hacker/0_5.jpg)

之后成功Create Database

![](/images/hacker/0_6.jpg)

数秒后自动转至Login界面，默认帐号为admin，密码为password

成功登陆DVWA

![](/images/hacker/0_7.jpg)



