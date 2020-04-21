---
title: Redis | 安装
date: 2020-04-21 21:32:20
categories:
- redis
tags:
- redis
---
目前，这里的安装方式有 Macbook。

<!-- more -->

<br/>

# MacBook

<br/>

使用 brew 进行安装

	brew search redis 

出现

	==> Formulae
	hiredis                                  redis                                    redis-leveldb                            redis@3.2                                redis@4.0
	==> Casks
	another-redis-desktop-manager   

然后我们安装

	brew install redis@4.0

## 使用

	brew services start redis@4.0 开启
	brew services stop redis@4.0 关闭