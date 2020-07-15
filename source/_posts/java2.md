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

# 简介

<br/>


