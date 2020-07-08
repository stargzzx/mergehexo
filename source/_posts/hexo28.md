---
title: hexo | command not found hexo
date: 2020-07-08 13:41:52
categories:
- hexo
tags:
- hexo
---
近期安装完 java 的项目后，在一小时之前成功执行的 hexo ，再次执行出现

	zsh: command not found: hexo

查阅资料都说是环境变量问题。

<!-- more -->

首先看看是不是 `npm`、`node` 的错误

	npm -v
	node -v

如果都不是，可能是环境变量的问题，但是，网上没人说是哪里出现的问题，于是，重新执行下面的命令，之后成功

	npm install hexo-cli -g

这个命令在 `~/blog` 下执行。

## 猜测

我认为环境变量是下面的这个。

我的博客根目录是 `~/blog`

然后，当我 `install` 包的时候，会将包安装到当前目录的 `node_modules` 中

所以，路径应该是

	~/bin/blog/node_modles/.bin

所以，要把这个路径放到环境变量中。