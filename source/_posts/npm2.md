---
title: nvm | node 和 npm 的版本控制器
date: 2020-09-20 00:05:35
categories:
- 网站设计
- npm
- nvm
tags:
- nvm
---
近期，我使用 `node 12` 的版本创建了很多 `hexo` 网站，但是，用 `gitbook` 的时候，出现 `node` 版本不兼容，经过查询得知，我可以使用 `nvm` 作为切换 `node` 的版本控制器。

<!-- more -->

<br/>

# 参考资料

<br/>

- [nvm](https://github.com/nvm-sh/nvm#install-script)
- [使用 nvm 管理不同版本的 node 与 npm](https://www.runoob.com/w3cnote/nvm-manager-node-versions.html)

<br/>

# 系统环境

<br/>

- ubuntu 18.04

<br/>

# 安装

<br/>

	sudo apt-get install build-essential
	wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash

`wget` 的命令，我推荐你使用官方最新的版本，请参考。

- [nvm](https://github.com/nvm-sh/nvm#install-script)

<br/>

# 命令

<br/>

- `nvm ls-remote` 列出远程服务器上所有的可用版本
- `nvm install 4.2` 安装指定版本
- `nvm use 4.2.2` 切换到某一版本
- `nvm use node` 切换到最新版
- `nvm alias awesome-version 4.2.2` 给某一版本其名字
- `nvm use awesome-version` 切换到某一其名字的版本
- `nvm unalias awesome-version` 取消名字
- `nvm ls` 列出已安装的实例
