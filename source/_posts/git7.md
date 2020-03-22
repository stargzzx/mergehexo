---
title: GIT | 其他包括基础、技巧等
date: 2020-03-22 17:07:20
categories:
- git
tags:
- git
---
这个是对 git 的收尾工作，里面记录了一些对于 git 很重要的知识，也有一些技巧。

<!-- more -->

<br/>

# \.git的目录

<br/>

这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

<br/>

# 用户 git 配置文件

<br/>

当前用户的Git配置文件放在用户主目录下的一个隐藏文件 \.gitconfig 中：

	[alias]
	    co = checkout
	    ci = commit
	    br = branch
	    st = status
	[user]
	    name = Your Name
	    email = your@email.com

<br/>

# 仓库配置文件

<br/>

每个仓库的Git配置文件都放在\.git/config文件中。

	[core]
	    repositoryformatversion = 0
	    filemode = true
	    bare = false
	    logallrefupdates = true
	    ignorecase = true
	    precomposeunicode = true
	[remote "origin"]
	    url = git@github.com:github_name/origin_repo.git
	    fetch = +refs/heads/*:refs/remotes/origin/*
	[branch "master"]
	    remote = origin
	    merge = refs/heads/master
	[alias]
	    last = log -1

<br/>

# \.gitignore


<br/>

如果，我们再上一步回撤的文件过多，未免工作量太多，并且，我们需要有些文件，是需要自动忽略，不上传的，比如log 文件，本地配置文件等。

有时候我们提交自己建的项目中想忽略某些文件夹可以建一个\.gitignore目录，里面指定想要忽略的文件或目录，比如自己eclipse的项目可以设置忽略bin/和gen/目录，这样就表示把 bin 目录和 gen 目录下的所有文件都忽略掉,从而使用得它们不会加入到版本控制当中

如下图所示：

![](/images/bbsx/2_3.jpg)

<br/>

# 上传时机

<br/>

这件事我和陈哥讨论过，究竟什么时候将本地代码 push 上去，陈哥给的建议是，只要有一点有效的修改，就要提交。关于这点陈哥的解释如下：如果时间拖得过长，那么版本库就会变得越来越大，而你的本地代码就会和版本库差别的越来越大，可能就会导致冲突，或者目录的变更，对维护产生很大的影响。

<br/>

# 查看状态

<br/>

	git status

每次执行完一个 git 命令，最好都执行一遍这个命令。

<br/>

# 查看之前的版本内容

<br/>

我们使用 git log 命令来查看过往版本的内容。

![](/images/bbsx/2_1.png)

然后，我们复制想要看的版本的 commit，然后执行

	git show ***
	
![](/images/bbsx/2_2.png)

<br/>

# git stash

<br/>

## 紧急BUG修复

当你在写代码的时候，突然，线上出现了一个紧急的BUG，但是，你又不能停下你手中的工作，那么你可以执行下面的命令

	git stash

让 commit 指向你的上一个commit，这样的话，你可以直接先修改BUG，然后提交，提交的是你修改BUG的代码，当你，修改完成后，可以再次执行

	git stash pop
	
这样，你又回到了之前的工作状态。

## stash 其他

	git stash save “修改的信息"

给 stash 进行标注。

	git stash list

列出 stash 的列表。

	git stash clear

清空 stash 的列表。

## 指定提出 stash

	git stash list

<br/>

	stash@{0}: On order-master-bugfix: 22222
	stash@{1}: On order-master-bugfix: 22222

### 方法 1

	git stash apply stash@{1}

git stash apply stash@{id} 命令则会继续保存stash id。所以，我们还需要删除多余的 stash

	git stash drop stash@{id}

就可以回到保存的版本了。

### 方法 2


	git stash pop stash@{int}

git stash pop stash@{id}命令会在执行后将对应的stash id 从stash list里删除。
