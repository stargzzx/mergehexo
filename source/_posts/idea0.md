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

<br/>

# 让一个 gradle 项目跑起来

<br/>

使用 IDEA 跑一个 gradle 项目，主要有下面的要求。

- JAVA 的编译环境 JDK 14
- IDEA 的语法环境
- gradle 的编译环境

首先，我们不是新建一个 gradle 项目，而是导入 gradle 项目。

## 导入 gradle 项目

- 选择菜单栏的 `File->Open`。
- 在打开的窗口选择对应 `Gradle` 项目的 `build.gradle` 文件，然后点击`OK`。

![](/images/idea/0_2.png)

- 在 `Open Project` 窗口中，点击 `Open as Project`

## 配置 JDK 和语法环境

在这里值得注意的是，语法环境和 IDEA 的版本有关，比如 `language level` 14 以上需要 IDEA 2020 版本以上。

所以，建议你安装下载最新的 IDEA 环境。

### 下载相应版本的 JDK

- [java | 安装多版本 JDK](https://benpaodewoniu.github.io/2020/07/08/java1/)

### 配置JDK 和语法环境

进入 `project settings` 在 「`file -> project structure` 查询」

![](/images/idea/0_3.png)

然后，左边红框的那些，都点一点，然后都换成相关的 JDK 和 对应 JDK 版本的 language level 。

![](/images/idea/0_4.png)

## 配置 gradle 环境

因为是 gradle 项目，所以，我们使用 gradle 来进行编译，如何配置这个，请参考。

- [gradle | 安装和简介](https://benpaodewoniu.github.io/2020/07/08/java2/)


