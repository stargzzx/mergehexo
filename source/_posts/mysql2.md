---
title: 服务器数据下载到本地
date: 2019-10-27 16:58:19
categories:
- [sql,mysql]
- [运维]
tags:
- mysql
- 数据库
- scp
---
如题。

<!-- more -->

因为我的情况比较特殊，所以，我把数据库的数据下载到本地测试是经过两个步骤，第一是把服务器的数据库数据放在服务器上，第二是在本地下载这个文件。

## 备份

### 参考资料

[MySQL 导出数据](https://www.runoob.com/mysql/mysql-database-export.html)

mysqldump 是 mysql 用于转存储数据库的实用程序。它主要产生一个 SQL 脚本，其中包含从头重新创建数据库所必需的命令 CREATE TABLE INSERT 等。

使用 mysqldump 导出数据需要使用 --tab 选项来指定导出文件指定的目录，该目标必须是可写的。

以下实例将数据表 runoob_tbl 导出到 /tmp 目录中：

```python
$ mysqldump -u root -p --no-create-info \
            --tab=/tmp RUNOOB runoob_tbl
password ******
```

### 导出 SQL 格式的数据

这里有一个需要注意的是

执行下面的命令，我们不需要进入 mysql 的环境。

导出 SQL 格式的数据到指定文件，如下所示：

```python
$ mysqldump -u root -p RUNOOB runoob_tbl > dump.txt
password ******
```

如果你需要导出整个数据库的数据，可以使用以下命令：

```python
$ mysqldump -u root -p RUNOOB > database_dump.txt
password ******
```

如果需要备份所有数据库，可以使用以下命令：

```python
$ mysqldump -u root -p --all-databases > database_dump.txt
password ******
```

当然，那份参考资料上还有直接备份到本地数据库，由于我没用到，所以在这里就不写了，感兴趣的小伙伴可以看看。

### 将文件导入到本地数据库

执行下面的命令不需要进入 mysql 的环境。

当我们拿到相关的文件后，我们可以执行下面的命令来替换掉本地数据库

	mysql -u root -p eos_voter < t_claim_info.txt

这里有几个注意点需要说明，其中 eos_voter 是本地的数据库名字，如果，你从服务器导出的是一张特定表的数据，那么，我们也不需要指定是那张表，只需要指定数据库就好了。

![](/images/mysql/2_0.png)

### 将文件下载到本地

下载服务器上的文件夹

	scp -r root@xxx.xx.xxx.xx:/root/htmltitle /Users/xxxxx/Desktop
	
下载服务器上的文件

	scp root@139.59.250.52:/root/htmltitle/htmltitle.cpp /Users/xxxxx/Desktop

指定某一端口下载文件

	scp -P 13128 root@123.123.123.123：/root/eos.txt ~

这里要格外留意的是命令中的 P 是大写，这和 ssh 连接的时候是不同的。

关于 ssh 你可以看我下面的博文。

[币币生息|远程服务器部署](https://benpaodewoniu.github.io/2019/10/09/bbsx5/)

从本地传到服务器只需要反过来写即可

	scp /Users/xxxxx/Desktop/htmltitle.cpp root@139.59.250.52:/root/htmltitle