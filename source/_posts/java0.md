---
title: JAVA | 安装 jdk8
date: 2020-04-21 15:45:33
categories:
- java
- 基础
tags:
- java
- jdk8
---
我将从 MacBook 和 ubuntu 两个角度来介绍如何安装 jdk8 。

<!-- more -->

<br/>

# MacBook

<br/>

你可以看我下面的文章来了解整个过程。

[MacBook | brew 本地安装](http://localhost:4000/2020/02/10/macbook8/)

<br/>

# ubuntu

<br/>

## 查找合适的openjdk版本:

	apt-cache search openjdk

输入以上命令后会列出很多版本, 选择自己需要的即可:

	截取了部分
	openjdk-7-jre-dcevm - Alternative VM for OpenJDK 7 with enhanced class redefinition 
	openjdk-7-jre-lib - OpenJDK Java runtime (architecture independent libraries) 
	openjdk-7-jre-zero - Alternative JVM for OpenJDK, using Zero/Shark 
	openjdk-8-dbg - Java runtime based on OpenJDK (debugging symbols) 
	openjdk-8-demo - Java runtime based on OpenJDK (demos and examples) 
	openjdk-8-doc - OpenJDK Development Kit (JDK) documentation 
	openjdk-8-jdk - OpenJDK Development Kit (JDK) 
	openjdk-8-jre - OpenJDK Java runtime, using Hotspot JIT 
	openjdk-8-jre-headless - OpenJDK Java runtime, using Hotspot JIT (headless) 
	openjdk-8-jre-jamvm - Alternative JVM for OpenJDK, using JamVM 
	openjdk-8-jre-zero - Alternative JVM for OpenJDK, using Zero/Shark 

## 安装

	sudo apt-get install openjdk-8-jdk

## 配置环境变量, 编辑如下文件:

	vim ~/.bashrc

因为我这台电脑安装过 jdk 所以，我直接使用了原来的配置，下面的我就没再执行

在最后一行加:

	export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
	export PATH=$JAVA_HOME/bin:$PATH
	export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

## 测试jdk是否安装成功:

	java -version

显示如下结果, 则说明安装成功:

	openjdk version "1.8.0_242"
    OpenJDK Runtime Environment (build 1.8.0_242-8u242-b08-0ubuntu3~16.04-b08)
    OpenJDK 64-Bit Server VM (build 25.242-b08, mixed mode)