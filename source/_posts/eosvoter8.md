---
title: eosvoter | 安全 v1 
date: 2020-02-29 13:31:56
categories:
- eosvoter
tags:
- eosvoter
password: eosvotereosvoter8
abstract: eosvoter 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
一个项目的安全是必不可少的，这个主要是由运维小哥做，我在这里就我的观察写一下吧。

<!-- more -->

目前，项目部署在 B 机器上，那么只有 A 机器才能登录 B 机器，我们称 A 为跳板机。

运维小哥在跳板机上给予其他人登录权限，这个登录权限并不是建立一个用户，而是将 ssh key 放在机器上。

所以，想要登录项目机器，首先你得有登录跳板机的权限。
