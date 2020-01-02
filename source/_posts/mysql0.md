---
title: 安装 mysql
date: 2019-03-18 21:22:50
categories:
- mysql
tags:
- mysql
- 数据库
---
这是安装 mysql 的基础教程。
<!-- more -->
## 参考资料
[net start mysql 发生系统错误2](https://blog.csdn.net/srhlwdamon/article/details/46684399)
[mysql下载与安装](https://www.cnblogs.com/tyhj-zxp/p/6693046.html)
[安装mysql Install/Remove of the Service Denied!错误的解决办法](https://blog.csdn.net/rainustop/article/details/81202558)
[Ubuntu 安装mysql和简单操作](http://www.cnblogs.com/zhuyp1015/p/3561470.html)
[navicat连接阿里云mysql出现1045](https://blog.csdn.net/weixin_38885605/article/details/84312411)
[MySql5.7创建数据库与添加用户、删除用户及授权](https://www.cnblogs.com/rimmy/p/7679076.html)
[navicat连接阿里云mysql出现1045](https://blog.csdn.net/weixin_38885605/article/details/84312411)
[MySQL新建用户,授权,删除用户,修改密码](http://www.cnblogs.com/analyzer/articles/1045072.html)
[远程连接阿里云服务器出现1045-Access denied for user 'root'](https://www.imooc.com/article/36342)
[navicat连接mysql时出现1045错误的解决方法](https://www.jb51.net/article/104992.htm)
[关于远程连接2003-can't connect to MYSQL server on ''10060](https://blog.csdn.net/f056917/article/details/78559625)
[Navicat远程连接阿里云服务器的mysql](http://www.cnblogs.com/Alier/p/8520770.html)
[利用Navicat连接阿里云服务器中MySQL](http://www.cnblogs.com/NeverCtrl-C/p/8884288.html)
[pymysql模块使用教程](https://www.cnblogs.com/sui776265233/p/9353148.html)
[Linux启动/停止/重启Mysql数据库的方法](http://www.cnblogs.com/linjiqin/p/3544472.html)
[Ubuntu 安装mysql和简单操作](http://www.cnblogs.com/zhuyp1015/p/3561470.html)
[Ubuntu 16.04 上安装 MySQL 5.7 教程](https://www.linuxidc.com/Linux/2017-05/143864.htm)
[Ubuntu安装MySQL数据库](https://www.cnblogs.com/yu-hailong/p/7629271.html)
[Ubuntu安装配置MySQL数据库](https://blog.csdn.net/likemecch/article/details/7731698)
[mysql不能存汉字](https://blog.csdn.net/d_hyacinth/article/details/54233515)
我们会分为两个系统的安装教程，一个是win10，一个是ubuntu16.04
## win10安装过程
[下载地址](https://www.cnblogs.com/tyhj-zxp/p/6693046.html)
进入下载页面，选择下方图片方式。
{% img /images/mysql/0_0.png %}
{% img /images/mysql/0_1.png %}
{% img /images/mysql/0_2.png %}
我们选择系统，然后下载 zip 压缩文件，压缩文件下载小的那个就行，我们仅仅只是需要它的 server 而已。
之所以下载 zip 是因为我们可以选择安装路径，而安装包是默认下到 c 盘的。
### 配置过程
解压MySQL压缩包
将以下载的MySQL压缩包解压到自定义目录下,我的解压目录是:
D:\mysql
将解压目录下默认文件 my-default.ini 拷贝一份，改名 my.ini
复制下面的配置信息到 my.ini 保存
如果没有my-default.ini,可自己新建my.ini
在 my.ini 复制下面内容

	[client]
	port=3306
	default-character-set=utf8
	[mysqld]
	port=3306
	character_set_server=utf8
	#安装路径
	basedir=D:\mysql
	#数据路径
	datadir=D:\mysql\data
	sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES
	[WinMySQLAdmin]
	D:\mysql\bin\mysqld.exe
	
### 添加环境变量
将 D:\mysql  D:\mysql\bin 添加到环境变量中
### 将mysql注册为windows系统服务
从控制台进入到MySQL解压目录下的 bin 目录下：
输入服务安装命令：
mysqld install MySQL --defaults-file="D:\mysql\my.ini"
安装成功后会提示服务安装成功。
#注：my.ini文件放在MySQL解压后的根目录下
#移除服务命令为：mysqld remove
### 启动MySQL服务
方法一：
启动服务命令为：net start mysql
方法二：
打开管理工具 服务，找到MySQL服务。
通过右键选择启动或者直接点击左边的启动来启动服务。
在安装 win10 的过程中确实有很多错误，但是搜搜都可以解决。
在这里先占个坑吧。


## ubuntu 安装过程
这个安装过程有很多坑，我记得不是太详尽了，只能靠大概的回忆，但是，遇到的问题都可以通过上面链接解决。
更为重要的是，我的安装平台是阿里云，这个坑是十分巨大的。
### 安装过程
输入

	sudo apt-get update
	sudo apt-get install mysql-server 
		也可能是 sudo apt-get install mysql-server mysql-client

在安装过程中需要你输入管理员（root）密码

	sudo mysql_secure_installation
	
上面的这个命令我没有执行，但是它的作用是 更改root密码、移除MySQL的匿名用户、禁止root远程登录、删除test数据库。使用上面的这些选项可以提高MySQL的安全。
输入

	sudo netstat -tap | grep mysql
	
这个命令是检查是否安装成功，如果看到有mysql 的socket处于 listen 状态则表示安装成功。
输入

	mysql -u root -p 
		-u 表示选择登陆的用户名， -p 表示登陆的用户密码
		
上面命令输入之后会提示输入密码，此时输入密码就可以登录到mysql。
### 管理命令
启动

	service mysql start　
	service mysqld start (5.0版本是mysqld)
	service mysql start (5.5.7版本是mysql)
	
停止

	service mysql stop
	
重启

	service mysql restart(5.5.7版本命令)
	
查看版本

	status;
	select version();

关于 mysql 的语法在这里就不介绍了，网上搜一大堆，但是优先推荐。
[MySQL 教程](http://www.runoob.com/mysql/mysql-tutorial.html)
### 配置阿里云
阿里云中有很多坑，仅仅上面的配置，是不能用的，外界冶炼解不了。
下面的步骤，我不确定哪个有用，但最好都试试。
{% img /images/mysql/0_3.png %}
{% img /images/mysql/0_4.png %}
找到mysql配置文件并修改

	sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
	#将bind-address=127.0.0.1注释
	
运行命令

	service mysql start
	mysql -uroot -p
	(输入密码)
	#进出mysql命令行操作界面输入一下
	grant all privileges on *.* to 'root'@'%' identified by 'mysql(将mysql替换成密码)' with grant option;
	flush privileges;
	exit;
		# .前第一个*是指数据库，代表所有数据库
		第二个*指数据库对象，代表数据库中所有对象
		'root'@'%' root是制定要授权的数据库用户
		%代表允许登录的IP
		mysql是你的数据库密码
		
重启mysql

	service mysql restart
	
防火墙开启3306端口

	sudo ufw allow 3306
	# 可能阿里云禁了3306这个端口，所以开启一下吧
	
在外界我是用 navicat 连接的。
输入相关的连接参数后出现
{% img /images/mysql/0_6.png %}
经过查询时阿里云需要添加安全组规则。
也就是开启 3306 端口
0.0.0.0/0代表所有ip都可以访问
{% img /images/mysql/0_5.jpg %}
图是网上找的，但是也差不多。
>出现1045-Access denied for user 'root'

这是没给 root 授权，也就是上面的那个命令写错了，所以可以进入 mysql 的控制界面，也就是

	mysql -u root -p
	
然后，执行

	GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '这里写自己数据库的密码' WITH GRANT OPTION;
	flush privileges;

然后连接发现仍然不成功。
听师弟的意思时，最好不要用 root 远程连接数据库，要自己创建一个用户。
先进入 mysql 的命令界面。
创建用户的命令
跟以往版本不同，MySQL5.7 mysql.user表没有password字段，这个字段改成了 authentication_string；
这里我们使用命令进行创建用户：

	CREATE USER 'username'@'host' IDENTIFIED BY 'password';
	
如创建一个test用户，密码为test123，可以进行远程登录：

	create user 'test'@'%' identified by 'test123';

username - 你将创建的用户名,
host - 指定该用户在哪个主机上可以登陆，此处的"localhost"，是指该用户只能在本地登录，不能在另外一台机器上远程登录，如果想远程登录的话，将"localhost"改为"%"，表示在任何一台电脑上都可以登录;也可以指定某台机器可以远程登录（即添加IP地址）;
password - 该用户的登陆密码,密码可以为空,如果为空则该用户可以不需要密码登陆服务器。
删除用户
如果用户创建错了，肯定要支持删除操作，使用命令：

	DROP USER 'username'@'host';
	
授权
授权test用户有testDB数据库的某一部分权限：

	grant select,update on testDB.* to test@'%' identified by 'test123';
	
授权test用户有testDB数据库的所有操作权限：

	grant all privileges on testDB.* to 'test'@'%' identified by 'test123';

授权test用户拥有所有数据库的某些权限：

	grant select,delete,update,create,drop on *.* to 'test'@'%' identified by 'test123';

privileges - 用户的操作权限,如select,delete,update,create,drop等(详细列表可自行百度)，如果要授予所有的权限可使用all（参考第二种授权方式）;% 表示对所有非本地主机授权，不包括localhost。
	
我一般用的是下面这个命令，即赋予 root 的权限。

	GRANT ALL PRIVILEGES ON *.* TO '用户名'@'%' IDENTIFIED BY '这里写自己数据库的密码' WITH GRANT OPTION;

赋予完权限后执行

	FLUSH PRIVILEGES; 
	
如果出现下面这个错误，也是因为权限不足，即可以用上面的命令进行授权。
{% img /images/mysql/0_7.jpg %}
## 其它
我们如果要存储中文，首先就要用 utf-8 的方式构建数据库。

	CREATE DATABASE <database_name> CHARACTER SET=UTF8;
