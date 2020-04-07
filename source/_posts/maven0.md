---
title: maven | 安装
date: 2020-04-07 12:03:39
categories:
- maven
tags:
- maven
---
近期，我在安装 zookeeper 的时候需要 maven 来构筑。

所以，这里讲述一下我的 maven 的安装经历。

<!-- more -->

<br/>

# 背景介绍

<br/>

- MacBook
- maven

<br/>

# 参考资料

<br/>

[官方下载](http://maven.apache.org/download.cgi)
[macbook安装maven](https://www.jianshu.com/p/22b7f4910edb)

<br/>

# 安装

<br/>

## 下载

使用

[官方下载](http://maven.apache.org/download.cgi)

下载。

![](/images/maven/0_0.png)

## 解压

在

	~

下，自己创建一个新的文件夹，比如我的是

	~/maven

然后，把上面的压缩包解压到 maven

	tar -xjvf ~/Downloads/apache-maven-3.6.3-bin.tar.gz -C ~/maven

## 配置环境变量

	vim ~/.bash_profile

添加以下两行：

	export M2_HOME=/Users/licong/maven/apache-maven-3.6.3
	export PATH=$PATH:$M2_HOME/bin

激活

	source ~/.bash_profile

## 检查 maven 是否已经安装成功

	mvn -v

![](/images/maven/0_1.png)

## 修改配置文件

进入配置文件夹

我的是

	~/maven/apache-maven-3.6.3/conf

然后

	vim settings.xml

### 修改存放jar包的本地仓库位置

	<localRepository>/path/to/local/repo</localRepository>

我没有修改，其默认的保存放路径在

	~/.m2/repository

### 修改镜像为阿里云的镜像

	<mirrors>
		<mirror>
		    <id>nexus-aliyun</id>
		    <mirrorOf>central</mirrorOf>
		    <name>Nexus aliyun</name>
		    <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
		</mirror>
		<mirror>
		  <!--This is used to direct the public snapshots repo in the
		      profile below over to a different nexus group -->
		  <id>nexus-public-snapshots</id>
		  <mirrorOf>public-snapshots</mirrorOf>
		  <url>http://maven.aliyun.com/nexus/content/repositories/snapshots/</url>
		</mirror>
		<mirror>
		    <id>nexus-osc-thirdparty</id>
		    <mirrorOf>thirdparty</mirrorOf>
		    <name>Nexus aliyun thirdparty</name>
		    <url>http://maven.aliyun.com/nexus/content/repositories/thirdparty/</url>
		</mirror>
	</mirrors>

找到相应的标签替换就好了。