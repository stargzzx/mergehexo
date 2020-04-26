---
title: eosvoter | 爬取数据
date: 2020-04-22 12:08:44
categories:
- eosvoter
tags:
- eosvoter
password: eosvotereosvoter13
abstract: eosvoter 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
因为 eosvoter 这个项目是建立在 eos 投票的基础上的，所以，很多数据都需要从主网或者其他地方获取。

但是，爬虫最大的劣势就是容易被封，所以，有了下面的技术升级路径。

<!-- more -->

<br/>

# 切换端口

<br/>

由于部署服务设备的机器是阿里云，大部分的项目都会封锁阿里云的地址。比如

- eos 主网
- dfuse 接口

所以，我只能不断的寻找合适的节点来获取上面的信息，比如

- eoslaomao
- eosbeijing

但，有时候请求过于频繁就会导致 IP 被封，所以，目前的一个临时方案就是下面的。

<br/>

# 启用另一个服务器

<br/>

本来打算使用代理的，但是，大部分免费的 IP 都不能有效的使用，要么短效、要么无效，所以，这一步就没弄。

然后，发现另外一台服务器可以获得相关的数据，于是就想着使用另外一台服务器获取数据，然后写到原来服务器的数据库中。

你可以查看我下面的文章。

[MySQL | 连接远程数据库](https://benpaodewoniu.github.io/2020/04/22/sql8/)

但这个只是暂时的方法，所以，现在的首要任务是建立自己的 IP代理池。

所以有了下面的步骤。

<br/>

# IP 代理池

<br/>

你可以查看我下面的专栏。

[proxy_pool](https://benpaodewoniu.github.io/categories/开源项目/python/proxy-pool/)