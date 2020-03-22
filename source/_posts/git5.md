---
title: GIT | 回退
date: 2020-03-22 15:14:34
categories:
- git
tags:
- git
---
这里是 GIT 的后悔药系列。

<!-- more -->

<br/>

# 工作区回退

<br/>

使用

	git checkout -- filename

或者

	git restore filename

<br/>

# 暂存区回退

<br/>

使用

	git reset --mixed filename [回退某一个文件]
	git reset --mixed HEAD~1 [回退整个暂存区]
		其就等于 git reset --mixed .

或者使用

	git restore --staged filename [回退某一个文件]
	git restore --staged . [回退整个暂存区]

<br/>

# 版本库回退

<br/>

关于 reset 的用法，你可以看我下面的博文。

[GIT | 区域知识](https://benpaodewoniu.github.io/2020/03/22/git3/)

## 将代码回退到暂存区

	git reset --soft HEAD~1

## 将代码回退到工作区

	git reset --mixed HEAD~1

## 将代码回退到某一个版本

	git reset --hard HEAD~1
	或者
	git reset --hard commit_id
	git restore --staged HEAD~1
