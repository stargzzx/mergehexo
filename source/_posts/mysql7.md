---
title: mysql | 免密输入
date: 2020-03-16 17:29:59
categories:
- mysql
tags:
- mysql
- 免密
---
这个真的是刚需，尤其是近期，大量的数据库操作搞的我每次输入密码都要 40 秒，严重影响了我的效率。

而且，新版的 mysql 在命令行直接明文写密码已经不允许。

并且，近期要在 jenkins 中自动导入数据库，所以，免密是一定要做的。


<!-- more -->

生成免密文件

	mysql_config_editor  set --login-path=licong --host=localhost --user=root --password

这个 licong 就是我们的使用凭证。

该命令会在本用户的目录下，生成一个

	.mylogin.conf

这个文件里面存放着我们的加密后的密码。

我们可以使用下面的 

	mysqlpump --login-path=liao mydatabase > bak.sql

进行免密导出。