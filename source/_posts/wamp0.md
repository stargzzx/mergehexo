---
title: wamp安装和启动
date: 2018-07-25 11:59:41
categories:
- wamp
tags:
- wamp
- tutorial
---
wamp 是一个集成环境，它有 apache + PHP + MySQL

<!-- more -->

## 安装过程

只需要有一点要注意，在安装过程的一个界面中有一个是选择默认浏览器的

## 遇到的问题

### 图标为红色

图标为红色，说明相关的服务没有打开。

对 计算机(右键)——>选择管理——>再选择服务和应用程序一栏中的服务。

看 wampapach 和 wampmysqld 是否开启。

假如在开启 wampmysqld 失败后，应该检查电脑中是否有已经开启过的 MYSQL 程序，如果有应该停止，再次开启 wampmysqld。

### 图标为黄色

黄色图标基本上都是端口占用问题，一般 wamp 默认接口是 80，浏览器的接口也是 80，所以冲突。

解决方案：左键点击 wamp 图标——>在 apache 目录下选择 httpd.conf 文件——>打开后，转到 Listen 80 这一行，将其改为 Listen 8080。
