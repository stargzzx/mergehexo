---
title: MySQL | 连接远程数据库
date: 2020-04-22 12:11:15
categories:
- sql
- mysql
tags:
- sql
- mysql
---
近期，由于 eosvoter 爬取数据过于频繁，导致，该服务器上的 IP 被封。

所以，选了其他服务器上跑程序，然后，把数据写到原来的服务器上。

<!-- more -->

<br/>

# 开启 MySQL 的远程连接

<br/>

MySQL 默认是不允许远程连接的，所以，在开始之前，要打开原来服务器数据库的远程连接权限。

登陆到服务器上

	show databases;

你可以看到有一个叫做 mysql 的数据库，然后

	use mysql;

然后，执行

	select host,user from mysql;

我的数据如下：

|host|user|
|---|---|
| localhost | mysql.infoschema|
| localhost | mysql.session|
| localhost | mysql.sys|
| localhost | root|

注意：此处的 "localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录。如果想远程登录的话，将 "localhost" 改为 "%"，表示在任何一台电脑上都可以登录。也可以指定某台机器可以远程登录。

可以使用

	update user set host="%" where user="root";

然后强制刷新：

	flush privileges; 

|host|user|
|---|---|
| localhost | mysql.infoschema|
| localhost | mysql.session|
| localhost | mysql.sys|
| % | root|

在网上流行另外一种方法，即赋予 root 权限的方法，使用下面的命令：

	授权格式：grant 权限 on 数据库. to 用户名 @登录主机 identified by "密码";**　 

其实，最终的效果是一样的，我个人感觉不如 update 来的快。

<br/>

# 编写 python 脚本连接

<br/>

## 测试

首先我们要想确定能不能连接另一个服务器的数据库，使用下面的命令，利用 ssh 连接试一下

	mysql -h 服务器IP -P 服务器端口 -u root -p

如果可以连接成功，那么就没有问题，使用上面试一下，主要有以下的原因。

- 防止原始服务器有白名单机制，本身就不支持外部服务器连接
- 防止原始服务器开启了 MySQL 的远程连接
- 测试是否可以成功连接

## python 和 MySQL 版本对应

有的 MySQL 不支持或者只支持固定版本的 python 连接。

[数据来源](https://dev.mysql.com/doc/connector-python/en/connector-python-versions.html)

|Connector/Python Version|	MySQL Server Versions|	Python Versions|	Connector Status|
|---|---|---|---|
|8.0|	8.0, 5.7, 5.6, 5.5|	3.8, 3.7, 3.6, 3.5, 3.4, 2.7|	General Availability|
|2.2 (continues as 8.0)|	5.7, 5.6, 5.5|	3.5, 3.4, 2.7|	Developer Milestone, No releases|
|2.1|	5.7, 5.6, 5.5|	3.5, 3.4, 2.7, 2.6|	General Availability|
|2.0|	5.7, 5.6, 5.5|	3.5, 3.4, 2.7, 2.6|	GA, final release on 2016-10-26|
|1.2|	5.7, 5.6, 5.5 (5.1, 5.0, 4.1)|	3.4, 3.3, 3.2, 3.1, 2.7, 2.6|	GA, final release on 2014-08-22|

## python 脚本编写

举一个跟简单的例子

```python
import pymysql.cursors

# 连接数据库
connect = pymysql.Connect(
    host='IP',
    port=3306,
    user='root',
    passwd='pw',
    db='eos_voter',
    charset='utf8'
)

print(connect)
```