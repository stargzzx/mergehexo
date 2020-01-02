---
title: MongoDB
date: 2019-07-03 22:02:39
categories:
- database
tags:
- database
- 数据库
- mongodb
---
这是mongodb的基础。
<!-- more -->
# 参考资料
[MongoDB下载与配置+可视化界面](https://www.jianshu.com/p/b0d6f3633f8e)
[win10安装MongoDB提示 the domain,user name and/or password are incorrect. Remember to use "." for the domain if the account is on the local machine.](https://www.cnblogs.com/zinan/p/10549405.html)
[MongoDB启动服务出现“服务没有响应控制功能”错误](https://blog.csdn.net/SL_World/article/details/82181731)
[MongoDB安装过程中出现service MongoDB failed to start，verify that you have sufficient privileges to start...](https://www.cnblogs.com/whowhere/p/9635956.html)
[MongoDB下载、安装和配置教程](https://blog.csdn.net/winstonlau/article/details/79439223)
[MongoDB数据库安装及配置环境windows10系统](https://www.cnblogs.com/gjw-hsf/p/7308822.html)
[python中安装包出现Retrying  Retry total=4, connect=None, read=None, redirect=None, status=None …………](https://blog.csdn.net/qq_25964837/article/details/80295041)
# 过程
我在安装Mongodb的时候作死，导致出现了很多很多坑，所以，在这里记录一下。
## 安装MongoDB
到官网上下载相关的文件，我下载的是 msi 格式。
[官网](https://www.mongodb.com/download-center/community)
### 安装时候的坑
#### 设置名字错误
在安装的时候，我选择了 custom，然后在下面我又选择了数据库仅用于当地（这个步骤是自己主动选择的），并给与重新命名。
结果出现这个错误。
{% img /images/database/0_0.png %}
这个解决方案是重新安装，选择 custom 后，路径自己配置，然后全部默认就好了。
#### 界面化安装
我再安装的过程中，有一个选项是
{% img /images/database/0_1.jpg %}
下一步安装 "install mongoDB compass" 不勾选，否则可能要很长时间都一直在执行安装，MongoDB Compass 是一个图形界面管理工具，我们可以在后面自己到官网下载安装。
[下载地址](https://www.mongodb.com/download-center/compass)
由于我勾选了这个，所以安装了很久都没有安装成功。我有点烦躁，就把安装的进程强行关了，打算重新安装一遍。
将这个取消勾选后，我重新安装，结果出现了这个错误。
{% img /images/database/0_2.png %}
##### 解决方法
直接点Ignore（忽略），然后进入安装MongoDB的目录下，进入data，新建一个文件夹然后命名为db。
注意
路径最好是全英文。
配置数据库和连接数据库

	进入命令行窗口后
	进入安装MongoDB的文件夹
	进入bin文件夹
	输入——–mongod -dbpath d:\'这是你安装mongoDB的文件夹，根据你自己的命名来写'\data\db
		我的是 mongodb d:\mongodb\data\db
		
{% img /images/database/0_3.png %}
我的不是上面那个界面，但是也差不多。

	继续在bin文件夹里鼠标右键–>打开命令行窗口，键入 mongo
	此时就成功连接数据库了。
	如图所示

{% img /images/database/0_4.png %}
这时，上上面的那个就变成
{% img /images/database/0_5.png %}
到这里，就成功连接啦！
## 开启mongodb
在路径上添加 mongodb 的bin 文件夹。
### 启动服务

	net start MongoDB

结果出现了这个错误：
{% img /images/database/0_6.png %}
这是因为 cmd 没有用管理员权限打开的结果。
用管理员权限后：
出现：

	服务没有响应控制功能
	
这是因为有一个命令没有执行

	mongod --dbpath "D:\MongoDB\data\db" --logpath "D:\MongoDB\data\log\mongo.log" --install --serviceName "MongoDB"
	
这个时候提醒我，没有 mongo.log 文件，于是，我在相关目录下重新建了一个。
执行上面的命令后，显示正常。
#### 首先确保上述路径中data和log文件夹已建好
{% img /images/database/0_7.png %}
#### 输入sc delete MongoDB命令
输入如下命令即可

	sc delete MongoDB
	
{% img /images/database/0_8.png %}
#### 如何判断mongo服务已经开启
"win+r" 进入“运行”界面，输入“services.msc”进入"服务"——> 如图
{% img /images/database/0_9.png %}
## 可视化安装
[下载地址](https://www.mongodb.com/download-center/compass)
这个只要选好路径，一路NEXT就好了。

