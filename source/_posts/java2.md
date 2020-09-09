---
title: gradle | 安装和简介
date: 2020-07-08 14:13:53
categories:
- java
- 基础
- gradle
tags:
- gradle
---
`gradle` 是 java 的包管理、编译、发布工具。

<!-- more -->

<br/>

# 安装

<br/>

首先，我要说明的是，我的环境是

- MacBook

因为当时把 `brew` 进行了换源「清华源」，然后安装 `gradle` 的版本是 `6.2`。

这个版本，配合 `JDK 14` 会出现编译错误，所以我们需要升级 `gradle`。

但是，清华源由于更新不及时，所以我们需要将源改回来，请参考我下面的博文。

- [macbook | brew 换源](https://benpaodewoniu.github.io/2020/02/10/macbook9/)

由于，我已经下载了 `gradle` 所以，我只需要更新一个这个包就好了

	brew upgrade gradle

发现确实正在从 6.2 更新到 6.5

但是，下载总是非常缓慢，于是，使用迅雷，瞬间下载完成，然后，使用 brew 的本地安装方法。

参考，我下面的博文。

- [MacBook | brew 本地安装](https://benpaodewoniu.github.io/2020/02/10/macbook8/)

如何在 idea 中配置 `gradle` 请看下面的博文。

- [idea | 面向小白](https://benpaodewoniu.github.io/2020/07/08/idea0/)

<br/>

# IDEA 使用 gradle

<br/>

## 导入 gradle 项目

如果是 gradle 项目，只需要直接打开就好， IDEA 会自行的创建 gradle 相关的环境。

## 创建 gradle 项目

我们在 IDEA 中，选择

	File ——> Project ——> Gradle

![](/images/gradle/2_0.png)

选好 java 的 SDK 和 java 项目。

![](/images/gradle/2_1.png)

选好项目目录。

这样就创建好了。

![](/images/gradle/2_2.png)

上面图中的东西会自己创建出来。

## 配置 gradle

假如，你一开始创建项目的时候，并没有 gradle ，但是，项目又是 geadle ，这个时候，你需要先安装 gradle ，然后，为该项目加上 gradle。

或者，你导入项目的时候 gradle 构建错了。

虽然，现在还不知道 `gradle` 到底是什么，但是，先把环境配置起来吧。

首先你要先把 `gradle` 在本地安装起来「参照上面」。

然后

	Preferences 搜索 gradle

在下面选择相应的选项。

![](/images/idea/0_1.png)

这里值得注意的是，我的 gradle 和 JDK 都是自己特指的，而不是默认的。 IDEA 有时候会默认 JDK 为 1.8 ，那个时候，配合 gradle 可能会出错。

第 1 指的是 `gradle` 的位置

在这里贴一下

	/usr/local/Cellar/gradle/6.5.1/libexec

第 2 指的是 `gradle` 的编译器是啥

我这里是 `open jdk 14`，路径如下:

	/Library/Java/JavaVirtualMachines/jdk-14.0.1.jdk/Contents/Home

## 寻找 jar 版本

- [mvnrepository](https://mvnrepository.com/)

