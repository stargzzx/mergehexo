---
title: macbook | brew 换源
date: 2020-02-10 00:43:53
categories:
- MacBook
tags:
- MacBook
- brew
---
这里是 brew 换清华源的教程。

<!-- more -->

因为换源具有时效性，所以，建议大家去官网看一下正版教程。

[Homebrew 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/homebrew/)

下面命令直接执行就好。

# 换成清华源

	git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

	git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

	git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git

	brew update

brew update 这一步挺慢的，耐心等待

# 重制为原来的源

	git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git

	git -C "$(brew --repo homebrew/core)" remote set-url origin https://github.com/Homebrew/homebrew-core.git

	git -C "$(brew --repo homebrew/cask)" remote set-url origin https://github.com/Homebrew/homebrew-cask.git

	brew update