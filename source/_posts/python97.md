---
title: python | 定时任务框架 apscheduler
date: 2020-05-20 09:45:02
categories:
- [python,模块,apscheduler]
- [python,进阶,定时任务框架]
tags:
- python
- apscheduler
---
在我参与的项目中，其中定时任务引擎就是基于 apscheduler 。

<!-- more -->

<br/>

# 参考资料

<br/>

- [花10分钟让你彻底学会Python定时任务框架apscheduler](https://zhuanlan.zhihu.com/p/46948464)

<br/>

# 引言

<br/>

说到定时任务，你会想起 linux 自带的 crontab ，windows 自带的任务计划，都可以实现守时任务。没错，操作系统基本都会提供定时任务的实现，但是如果你想要更加精细化的控制，或者说任务程序需要跨平台运行，最好还是自己实现定时任务框架，Python 的 apscheduler 提供了非常丰富而且方便易用的定时任务接口。本文介绍如何使用 apscheduler 实现你的定时任务。

apscheduler 使用起来十分方便。提供了基于日期、固定时间间隔以及crontab 类型的任务，我们可以在主程序的运行过程中快速增加新作业或删除旧作业，如果把作业存储在数据库中，那么作业的状态会被保存，当调度器重启时，不必重新添加作业，作业会恢复原状态继续执行。apscheduler 可以当作一个跨平台的调度工具来使用，可以做为 linux 系统crontab 工具或 windows 计划任务程序的替换。注意，apscheduler 不是一个守护进程或服务，它自身不带有任何命令行工具。它主要是要在现有的应用程序中运行，也就是说，apscheduler 为我们提供了构建专用调度器或调度服务的基础模块。

<br/>

# 安装

<br/>

安装非常简单，会用 pip 的人都知道

	pin install apscheduler

<br/>

# 基本概念介绍

<br/>

