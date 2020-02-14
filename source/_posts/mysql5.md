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

<br/>
# 只导出数据库结构
<br/>

	mysqldump -u root -p -d coinex >> coinex.sql

只需要加一个 -d 就可以只导出结构而不导出数据。

	mysqldump -u root -p -d coinex btc >> btc.sql

这个是导出 coinex 数据库中 btc 这张表的结构

<br/>
# 带有条件的数据库导出
<br/>

	mysqldump -u用户名 -p密码 -h mysql主机  --default-character-set=指定编码  数据库名称  表名称  --where=" 查询条件 " > 导出文件名.sql

例子：

	mysqldump -u root -p 12345678 -h 192.168.1.123  --default-character-set=utf8 mydbname mytablename --where=" sid = 123456 and name = '小明同学' " > my_export_data.sql

远程我没试过，我只试过本地的，而且，-p 后面跟着密码不管用，必须要手动输入。。。查了一下，貌似是新版本的 mysql 的安全机制，因为暂时对这个没要求，所以，就不研究免密导出了。

<br/>
# mysql8.0 导入 mysql5.7 的时候出错
<br/>

出现

	[ERR] 1273 - Unknown collation: 'utf8mb4_0900_ai_ci'

由于这两个版本的默认字符集不兼容，所以，导致，导入的时候出错。

解决的办法很简单。

解决方法：

打开sql文件，将文件中的所有

utf8mb4_0900_ai_ci 替换为 utf8_general_ci

utf8mb4 替换为 utf8

保存后再次运行sql文件，运行成功

有的时候，可能不是 utf8mb4_0900_ai_ci 和 utf8mb4，但也没关系，只要把相应位置的东西，进行匹配替换就好了。

{% img /images/mysql/5_0.png %}
