---
title: PyMySQL 操作数据库
date: 2019-03-18 22:29:33
categories:
- python
- 模块
- PyMySQL
tags:
- python
- python第三方库
- 数据库
- mysql
- pymysql
- 第三方库
---
论 python 如何操作 mysql 数据库。

<!-- more -->

## 参考资料

[python安装mysqldb模块失败，why？](https://segmentfault.com/q/1010000009777214)
[pymysql模块使用教程](https://www.cnblogs.com/sui776265233/p/9353148.html)
[MySQL 教程](http://www.runoob.com/mysql/mysql-tutorial.html)

## 2.7 和 3.x 的区别

在 2.7 中，模块名叫 MySQLdb

执行

	pip install MySQLdb
	
3.x 中，模块名叫 PyMySQL

执行

	pip install PyMySQL
	
## 用法

用法在上面的使用教程里面已经介绍的很详细了。

只要根据 mysql 的语法，然后按照上面的教程操作就行。

## 其它

首先，我们再建立数据库的时候，就要选定编码方式。

	CREATE DATABASE <database_name> CHARACTER SET=UTF8;
	
这样可以存储中文。