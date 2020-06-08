---
title: eosvoter | 关于项目的一些业务逻辑
date: 2019-10-09 21:00:39
categories:
- [项目经历,中型,eosvoter]
tags:
- eosvoter
- 币币生息
---
这个主要是怕忘记了业务逻辑，所以，在这里总结一下。

该文章的参考价值很少，仅仅只是流水账的记录，不成体系。

<!-- more -->

<br/>

# V1.0

<br/>

## 服务器

### 如何登录远程服务器

因为我们登陆的服务器需要通过一个服务器作为跳板，也就是你必须先登录服务器A，然后用服务器A来登陆服务器B。

登录服务器A的命令：

	ssh root@***** (地址已经加密)
	
用服务器A来登陆服务器B的命令是：（在服务器A的界面中输入）

	ssh root@*****

## 数据库关系

### 查看两个投票bp的关系

我们通过

	select * from t_settle_relation;
	
查看账户之间的关系。

![](/images/bbsx/4_0.png)

其中属性 pid 是 parent id ，也就是父亲节点，虽然说是父亲节点，但是并没有包含关系，只是关联关系，通过 pid 的话，进行串联，可以通过一个 bp_name 找到其它相关的 投票bp ，是一种不错的数据结构。

比如，id = 27 就和 id = 33 相关联，因为 id = 33 的 pid = 27.

那么我们怎么验证呢？

我们通过下面的这个命令

	select * from t_settle_account where settle_relation=27;

![](/images/bbsx/4_1.png)

我们从这一幅图中可以看到，每一个投票bp下面都有很多account。

也就是，如图中的例子

我们查找 blockpooleos 投 

## 脚本命令行

### 结算脚本


	python bp_settlement.py -from_bp big.one -to_bp eoslambdacom -start_time '2019-09-06 00:00:00' -end_time '2019-09-25 00:00:00'

以上为 `V1.0` 版本。

<br/>

# 更新 V1.1

<br/>

`V1.1` 版本的`币币生息COIN系统`不是之前那么麻烦的部署了，而是采用 `jenkins` 自动化部署的方案。

同时业务从单一的 `eos` 系统，变成了集合了矿池「主要是 BTC」的综合系统，也就是开启了监控矿池的模块。

