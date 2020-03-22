---
title: GIT | 分支
date: 2020-03-22 15:38:26
categories:
- git
tags:
- git
---
分支，GIT 的核心。

<!-- more -->

<br/>

# 创建分支

<br/>

	git branch 分支名

<br/>

# 删除分支

<br/>

	git branch -d 分支名

强制删除

	git branch -D 分支名

比如，我们新创建了一个分支，但是，最后这个分支没用了，所以，要删除，但是，这个分支并没有合并到 master ，所以，git 会产生疑问，这个时候你要使用强制删除。
<br/>

# 查看分支

<br/>

	git branch

<br/>

# 切换分支

<br/>

	git checkout 分支
	我更推荐你用下面的命令
	git switch 分支

<br/>

# 合并分支

<br/>

## 分支合并

我们切换到自己创建的分支，比如 bitmex 。然后，我们想把 master 的代码合并到 我们创建的分支上。

所以，我要先切换到 master，然后 pull ，保证 master 的代码是最新的。

然后，在切换到 bitmex 中，执行 

	git merge master[合并本地 master 分支]
	git merge origin master [合并远程 master 分支]

这个 merge 操作，是你在哪个分支，就会自动吧 merge 后面的分支和当前分支的代码进行合并。

## 合并分支实例

团队在写项目代码的时候，经常会建设多个分支。但是，最终，我们都是要回归 master 分支的。

在这里，我们有一个情况，就是，当团队成员将 master 分支代码修改之后，而你正在 update_li 这个分支上修改，所以，你虽然一开始是基于 master 创建的分支，但是，你现在的代码已经和 master 有了很大的不同。

这里，假设一种情况。如果，在你的分支上，你已经测试过无误之后，首先你将代码更新到 update_li 这个分支上，这个时候，你需要将你的代码合并到 master 上。以下是具体的步骤。

首先，要合并 master ，我们的原则是 master 的代码是可以稳定运行的代码，所以，冲突方面应该在自己的分支上解决。

我们先切换到 master 分支上，然后 git pull origin master ，我们将 master 的代码变成最新的代码。

### 在自己分支上解决完冲突

然后，我们切换到 update_li 这个分支上，我们使用

	git merge origin master

这个 origin 是指远程仓库，这句话的意思是 merge 远程仓库的 master，由于之前我们已经将本地的 master pull 到最新的代码，所以，直接用

	git merge master（merge 本地代码）

这条命令也可以，当然最好还是加上 origin

我们在 update_li 上解决完冲突后，使用 git status 看一下

这个时候，你会发现，有很多 文件需要我们 commit ，这是很正常的事，因为，你从 master 分支上，merge 了很多文件。这些文件都是你之前没有的，而且，你 merge 的话，就默认，这些文件属于你的，你只需要把这些文件提交到远程分支上就好了。

ok，现在我们就默认你已经将代码提交到 update_li 上了，在这里我需要重申一遍，update_li 上面保存的代码应该是 最新的 master + 你自己写的代码。

### 将解决完冲突的合并到 master

现在让我们切换到 master 上。

我们执行

	git merge origin update_li

这样，在 master 上，我们的代码就是已经处理完冲突之后的代码了。

这个时候，你再讲代码 git push origin master 就好了。

## Git合并指定文件到另一个分支

如果只想将feature分支的某个文件f.txt合并到master分支上

	git checkout feature 
	git checkout --patch master f.txt

第一个命令： 切换到feature分支；

第二个命令：合并master分支上f文件到feature分支上，将master分支上 f 文件追加补丁到feature分支上 f文件。你可以接受或者拒绝补丁内容。

如果只是简单的将feature分支的文件f.txt copy到master分支上；

	git checkout master
	git checkout feature f.txt

## 合并指定分支上的 commit

这个命令是

	git cherry-pick commit_id

因为 commit_id 是唯一的，所以不需要指定分支。

### 合并指定 commit 实例

假设有两个分支

- master
- dev

dev 我们新创建了一个 t.txt 的文件，我们对 t.txt 文件做了如下的修改

|commit_id|t.txt的内容|
|---|---|
|6786|1|
|7844|12|
|1231|123|

然后，我们切换到 master 分支，如果执行

	git cherry-pick 7844

那么，master 分支上会出现 t.txt ，并且里面的内容是 12

如果，我们后悔了这次合并的 commit

我们只需要执行

	git cherry-pick --abort

就好了。

