---
title: GIT | 远程相关
date: 2020-01-04 22:32:55
categories:
- git
tags:
- 远程仓库
- hexo
- git
---
这个对于理解 hexo 和 git 非常重要。

<!--more-->

远程仓库是我们的数据存储的地方，我们有以下命令可以查看和设置远程仓库.

<br/>

# 本地关联线上

<br/>

这个主要适用于在本地开发后，想要传到线上的 git 中，并且已经在线上创建了版本库，比如 origin_repo

所以，可以在本地的项目目录下执行

	git remote add origin git@github.com:github_name/origin_repo.git


推送内容

	git push -u origin master

由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

<br/>

# 查看远程仓库

<br/>

    git remote -v

可以显示出

    origin	git@github.com:benpaodewoniu/mergehexo.git (fetch)
    origin	git@github.com:benpaodewoniu/mergehexo.git (push)

<br/>

# 删除远程仓库

<br/>

    git remote rm origin

<br/>

# 修改远程仓库

<br/>

## 直接修改

    git remote set-url origin xxxxx.git

<br/>

# 使用线上版本强制覆盖本地版本

<br/>

	git fetch --all
	git reset --hard origin/master
