---
title: git 远程仓库的知识
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

远程仓库是我们的数据存储的地方，我们有以下命令可以查看和设置远程仓库

<br/>

# 查看远程仓库

<br/>

    git remote -v

可以显示出

    origin	git@github.com:benpaodewoniu/mergehexo.git (fetch)
    origin	git@github.com:benpaodewoniu/mergehexo.git (push)

<br/>

# 设置远程仓库

<br/>

    git remote add origin xxxxx.git

<br/>

# 删除远程仓库

<br/>

    git remote rm origin

<br/>

# 修改远程仓库

<br/>

## 直接修改

    git remote set-url origin xxxxx.git

## 先删除后修改


