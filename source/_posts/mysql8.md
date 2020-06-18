---
title: mysql | 修改密码
date: 2020-06-18 11:59:45
categories:
- sql
- mysql
tags:
- mysql
---
针对版本为 `8.0` 的 mysql。

<!-- more -->

<br/>

# 修改root用户的加密方式

<br/>

## 登录mysql。

	mysql -u root -p

通过执行以下命令查看对应用户的加密方式，也可以将user='root’改成比如user=‘test’,来查看用户test的加密方式。

	use mysql;
	select user,plugin from user where user='root';

## 执行命令修改加密方式

	alter user 'root'@'localhost' identified with mysql_native_password by '你的密码';

## 使配置生效

	flush privileges;

