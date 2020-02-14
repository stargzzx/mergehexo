---
title: mysql | 导出导入数据库
date: 2020-02-14 10:28:52
categories:
- mysql
tags:
- mysql
---
这个博文是对下面这个文章进行了更深层次的探讨。

你可以和它进行相互补充，谢谢～！

[服务器数据下载到本地](https://benpaodewoniu.github.io/2019/10/27/mysql2/)

<!-- more -->

# 带有条件的数据库导出

	mysqldump -u用户名 -p密码 -h mysql主机  --default-character-set=指定编码  数据库名称  表名称  --where=" 查询条件 " > 导出文件名.sql

例子：

	mysqldump -u root -p 12345678 -h 192.168.1.123  --default-character-set=utf8 mydbname mytablename --where=" sid = 123456 and name = '小明同学' " > my_export_data.sql

远程我没试过，我只试过本地的，而且，-p 后面跟着密码不管用，必须要手动输入。。。查了一下，貌似是新版本的 mysql 的安全机制，因为暂时对这个没要求，所以，就不研究免密导出了。