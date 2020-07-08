---
title: java | 安装多版本 JDK
date: 2020-07-08 11:04:15
categories:
- java
- 基础
tags:
- java
---
公司又有新的业务需求，我可能要从 python 转向 java 了。

之前在本机装了 JDK 8 但是，现在版本太低了，我要装 JDK 14，我查阅了相关的资料，在此做一个总结。

<!-- more -->

<br/>

# 参考资料

<br/>

- [How To Install OpenJDK 14 On Mac](https://java.tutorials24x7.com/blog/how-to-install-openjdk-14-on-mac)
- [MacOS 下安装多个版本的 JDK 并统一管理](https://zhuanlan.zhihu.com/p/78292060)
- [我的博文 | JAVA | 安装 jdk8](https://benpaodewoniu.github.io/2020/04/21/java0/)

<br/>

# 安装相关

<br/>

## 查看本机安装的 java

	/usr/libexec/java_home -V

可以看出，我现在就安装了 JDK 8.

	Matching Java Virtual Machines (1):
	    1.8.0_242, x86_64:	"AdoptOpenJDK 8"	/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
	/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home

## 查看现在使用的 java

	java -version

可以看出现在是 JDK 8

	openjdk version "1.8.0_242"
	OpenJDK Runtime Environment (AdoptOpenJDK)(build 1.8.0_242-b08)
	OpenJDK 64-Bit Server VM (AdoptOpenJDK)(build 25.242-b08, mixed mode)

## 安装 JDK 14

下载相关的软件

- [下载地址](https://jdk.java.net/14/)

根据系统环境下载相应的 `tar.gz` 文件。「openjdk-14.0.1_osx-x64_bin.tar.gz」

解压

	tar -xf openjdk-14.0.1_osx-x64_bin.tar.gz

会出现一个叫做 `jdk-14.0.1.jdk` 的文件。

假如说，你的机子里已经安装了 `JDK-14` 了，那么先删除「这个方式安装的 JDK 不会自动升级，每次都需要自己去删除然后重新安装对应的 JDK」

	sudo rm -rf "/Library/Java/JavaVirtualMachines/jdk-14.0.1.jdk"

假设已经删除或者没有，你可以执行

	sudo mv jdk-14.0.1.jdk /Library/Java/JavaVirtualMachines/

这个时候你，检查一下 java 的版本

	java -verison

会发现其自动切换到最新的 java 了

	openjdk version "14.0.1" 2020-04-14
	OpenJDK Runtime Environment (build 14.0.1+7)
	OpenJDK 64-Bit Server VM (build 14.0.1+7, mixed mode, sharing)

如果这个时候你再检查相关的 java 路径

	/usr/libexec/java_home -V

两个 java 都会出现

	Matching Java Virtual Machines (2):
	    14.0.1, x86_64:	"OpenJDK 14.0.1"	/Library/Java/JavaVirtualMachines/jdk-14.0.1.jdk/Contents/Home
	    1.8.0_242, x86_64:	"AdoptOpenJDK 8"	/Library/Java/JavaVirtualMachines/adoptopenjdk-8.jdk/Contents/Home
	/Library/Java/JavaVirtualMachines/jdk-14.0.1.jdk/Contents/Home

<br/>

# 切换不同的 java 版本

<br/>

	# List installed JDKs
	/usr/libexec/java_home -V
	# Switch active JDK
	export JAVA_HOME=`/usr/libexec/java_home -v <version>`
	# Example
	export JAVA_HOME=`/usr/libexec/java_home -v 1.8.0_251`

你也可以借助 zsh 来自行配置多个自动化识别版本，参考。

- [MacOS 下安装多个版本的 JDK 并统一管理](https://zhuanlan.zhihu.com/p/78292060)
- [MacBook下的z-shell](https://benpaodewoniu.github.io/2019/10/03/macbook4/)