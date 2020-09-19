---
title: gitbook | 基本用法
date: 2020-08-11 16:43:35
categories:
- gitbook
tags:
- gitbook
---
讲一下 `gitbook` 的基本用法。

<!-- more -->

<br/>

# 安装

<br/>

## 安装GitBook

	npm install gitbook -g
	npm install -g gitbook-cli
	--- 如果没有安装gitbook,此命令会默认同时安装 GitBook
	gitbook -V
	--- 列出本地所有的gitbook版本
	gitbook ls

<br/>

# 卸载

<br/>

	npm uninstall -g gitbook
	npm uninstall -g gitbook-cli
	--- 清除npm缓存
	npm cache clean -f

<br/>

# GitBook常用命令

<br/>

- `gitbook -V`      查看版本号
- `gitbook init`    初始化
- `gitbook serve`   预览
- `gitbook build`   生成
- `gitbook build --gitbook=2.6.7` 生成时指定gitbook的版本, 本地没有会先下载
- `gitbook uninstall 2.6.7`   卸载指定版本号的gitbook
- `gitbook fetch [version]`      获取[版本]下载并安装<版本>
- `gitbook --help`   显示帮助文档
- `gitbook ls-remote`  列出NPM上的可用版本

<br/>

# 其他

<br/>

有的时候，我们执行 `gitbook` 命令，会遇到各种错误，比如下面的

	if (cb) cb.apply(this, arguments)
	                 ^
	TypeError: cb.apply is not a function

这个是由于 `node` 版本不一致导致的，所以，我们要修改不同版本的 `node` 和 `npm`。

这个时候就用到 `nvm` 了。你可以参考我下面的博文

- []()

ps: 在 `gitbook` 版本为 `3.2.3` 左右的时候，我的 `node` 要在 `13` 以上。
