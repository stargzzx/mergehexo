---
title: coinex|数据库设计
date: 2019-11-19 21:28:36
categories:
- coinex
tags:
- 币币生息
- 项目
- coinex
---
这个讲述我的在币币生息所经历的项目——coinex。

这一节主要讲述数据库是怎么设计的，每一个数据表又有什么关联。

<!-- more -->

# 流程

t_stragegy_instance 表是目前的所有程序的开始，首先，我们先根据 instance_name 拿到 instance_id 和 strategy_id。

然后，根据 instance_id 在 t_relation这张表中查到 trade_user_id ，有时候我们会查到多个 trade_user_id。

然后，我们根据 trade_user_id 通过 t_trade_user 拿到相关的 key 和 secret。

同样，我们根据上面的 strategy_id 拿到相关的策略。

# 所有的表

{% img /images/coinex/0_0.png %}

# t_strategy

{% img /images/coinex/0_1.png %}

这个表是对应的策略层，其中 strategy_id 和 strategy_name 是我们要特别注意的。

这个 strategy_name 是由我们自己定义的。

# t_stragegy_instance

{% img /images/coinex/0_2.png %}

这个表目前我主要用的参数是 instance_id , strategy_id 和 instance_name

其中 strategy_id 对应的就是上面那张表 t_strategy 所对应的策略。

而， instance_name 是我们查询这张表的参数。

# t_trade_user

{% img /images/coinex/0_3.png %}

这个表主要存储了 app_key 和 app_secret ，我们还要注意 trade_user_id 和 user_id 。

关于，app_key 和 app_secret 的加密方式，我们是有两层加密的，最开始的一层是在

fcoin-x 项目中，我们需要先切换到 sort 分支下，然后找到 crypto_util.py

根据相应的运算，我们将得到的加密放在表的相应位置。

# t_relation

{% img /images/coinex/0_4.png %}

这张表很关键，它记载了 instance_id 到 trade_user_id 的映射。

