---
title: idea | 面向小白
date: 2020-07-08 14:08:35
categories:
- [java,IDE,idea]
tags:
- idea
---
关于如何下载 idea 你可以参考我下面的博文。

- [software | JetBrains全系列软件激活](https://benpaodewoniu.github.io/2020/03/11/software4/)

关于如何下载 JDK ，你可以参考我下面的博文。

- [java | 安装多版本 JDK](https://benpaodewoniu.github.io/2020/07/08/java1/)

<!-- more -->

假设你现在都已经做好准备了，那就开始我们真正的小白之旅。

先提前说明一下，环境是 `macbook`。

<br/>

# 更换 JDK

<br/>

一开始创建项目的时候，就要选择相应的 jdk ，如果后期需要改变这个 jdk 可以

	file -> Project Struture

选择相应的 jdk 即可。

![](/images/idea/0_0.png)

第 1 指的是 `gradle` 的位置

在这里贴一下

	/usr/local/Cellar/gradle/6.5.1/libexec

第 2 指的是 `gradle` 的编译器是啥

我这里是 `open jdk 14`，路径如下:

	/Library/Java/JavaVirtualMachines/jdk-14.0.1.jdk/Contents/Home
