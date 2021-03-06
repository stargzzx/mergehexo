---
title: eosvoter | 文档编写 v1
date: 2020-02-28 15:40:12
categories:
- [项目经历,中型,eosvoter]
tags:
- eosvoter
---
文档可以作为衡量一个项目是否完整的标准。

在初期，我们是没有文档的，只有一个 api 的接口说明，对于业务流程、部署、功能都没有详细的说明。

假设万一来了一个和我对接项目的人，那么，差不多会有两种选择，一个是让他通读代码，另一个是我不停的教他。

无论，哪一种都会浪费大量的时间，所以，日常编写好文档，让你的工作更好的交接给其他人。

<!-- more -->

我们的文档是在线文档，所用的平台是石墨文档。

这个平台的免费版就已经够用了。

我才用的是模块化的文档，将各种功能或者大类放在同一个文件夹中，然后，列一个总的目录。

<br/>

# 总展示

<br/>

![](/images/eos_voter/5_0.png)

<br/>

# 目录展示

<br/>

![](/images/eos_voter/5_1.png)

我的目录是这样分类的，希望可以帮到大家

	项目存在意义 
	项目名词解释  
	账号
	数据库设计说明  
	代码部署
	本地运行
	日志系统
	数据采集与处理脚本
	后台
	前端

这个在线文档在后面给我帮了很大的忙，一方面是我没有了交接工作的压力，另一方面，在部署一些任务的时候，我无需记在脑海里，只需要在文档中找一下就好了。

<br/>

# 文档书写原则

<br/>

- 行与行之间要留有余地，这样看起来不累
- 不同层级的内容应该严格使用不同的字体和类型
	- 内容：正文
	- 一级标题、二级标题 都要有严格的书写标准
- 要采用模块化书写
	- 利用标题分割成一个个的模块
- 尽量不使用图片
	- 图片会使排版凌乱，影响美感
- 要分清全局内容和私有内容，将其进行合理的组织
- 要有一个索引目录，从总索引开始，找到具体的文章，应该不能超过三个跳转
- 尽量不要贴外部链接，应该将所有的内容全部书写一遍，避免外链失效

你可以参考我下面的文章，来探究接口书写规范。

- [eosvoter | 接口规范 v1](https://benpaodewoniu.github.io/2020/02/28/eosvoter6/)