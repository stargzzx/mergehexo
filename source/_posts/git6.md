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

# 分支备注

<br/>

有的时候我们创建了大量的分支，最后发现有的分支是什么作用，有没有被合并都不知道了，所以，我们迫切的需要一个可以备注分支作用的命令。

	git config branch.{branch_name}.description "内容"

比如

	git.config branch.dev.description "升级部分数据"

## 查看

	git.config branch.dev.description

## 修改

	git.config branch.dev.description "修改的内容"

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

### git合并某一系列的 commit （没成功试过，暂时记录一下）

在一些特性情况下，合并单个commit并不够，你需要合并一系列相连的commits。这种情况下就不要选择cherry-pick了，rebase 更适合。还以上例为例，假设你需要合并feature分支的commit76cada \~62ecb3 到master分支。

首先需要基于feature创建一个新的分支，并指明新分支的最后一个commit：

	git checkout featuregit 
	git checkout -b newbranch 62ecb3

然后，rebase 这个新分支的 commit 到 master（--onto master）。76cada^ 指明你想从哪个特定的commit开始。

	git rebase --onto master 76cada^ 
		这个命令在 newbranch 执行

得到的结果就是feature分支的commit 76cada \~ 62ecb3 都被合并到了master分支。【我试了，但是没成功。。。】

### git merge 反悔，去掉一些 commit

[参考资料](https://www.zhihu.com/question/325639912/answer/695706958)

#### 情况 1

假如你有连续4个commit：

commit-id-1，commit-id-2，commit-id-3，commit-id-4

当你后悔搞了commit-id-3和commit-id-4之后，你可以反悔并且悄悄: 

	git reset commit-id-2。

意思就是说：“我后悔了，悄悄把我后面的脚印擦掉吧，只留到commit-id-2”。这样操作以后执行git log就不会看到你后面commit-id-3和commit-id-4的痕迹(表面上看不到，Git会给你留途径让你看到痕迹，这里不继续延伸了)。当然了建议git reset后面加一个--hard参数让你改写历史的时候工作区也跟上一起刷新，最后推荐的命令是

	git reset --hard commit-id-2。

#### 情况 2

假如你有连续4个commit：

	commit-id-1，commit-id-2，commit-id-3，commit-id-4

当你后悔搞了commit-id-3之后，你可以反悔并公开：

	git revert commit-id-3

这样操作以后你的commit树就变成了commit-id-1，commit-id-2，commit-id-3，commit-id-4，commit-id-5这个样子。

其含义是，你撤销了 commit-id-3 的操作，所以，你自动提交了 commit-id-5，也就是，假设 commit-id-3 在文件中插了一段代码，那么 commit-id-5 就是去掉这段代码。


#### reset 和 revert 的区别

还有一个最重要的区别就是：reset是“reset到xxx”；revert是“revert掉xxx”。

也就是说，reset处理的是一个区间，是从HEAD到你指定的commit-id之间的所有commit-id全部抹除，所以很省力；

revert一次命令只能处理一个commit-id，所以对于大区间来说会比较费劲。revert最主要的一件事情要理解：假如你站在大楼的10层，想把第8层和第9层干掉的话，那么先revert第9层，再revert第8层，原因自己分析吧。

<br/>

# 反悔合并

<br/>

	git merge --abort

<br/>

# 合并的类型

<br/>

## Fast-forward (—ff)

在当前分支相比于我们要合并的分支没有额外的提交（commit）时，可以执行 fast-forward 合并。Git 很懒，首先会尝试执行最简单的选项：fast-forward！这类合并不会创建新的提交，而是会将我们正在合并的分支上的提交直接合并到当前分支。

![](/images/git/6_0.gif)

完美！现在，我们在 dev 分支上所做的所有改变都合并到了 master 分支上。那么 no-fast-forward 又是什么意思呢？

## No-fast-foward (—no-ff)

如果你的当前分支相比于你想要合并的分支没有任何提交，那当然很好，但很遗憾现实情况很少如此！如果我们在当前分支上提交我们想要合并的分支不具备的改变，那么 git 将会执行 no-fast-forward 合并。

使用 no-fast-forward 合并时，Git 会在当前活动分支上创建新的 merging commit。这个提交的父提交（parent commit）即指向这个活动分支，也指向我们想要合并的分支！

![](/images/git/6_1.gif)

没什么大不了的，完美的合并！现在，我们在 dev 分支上所做的所有改变都合并到了 master 分支上。

<br/>

# 合并冲突

<br/>

尽管 Git 能够很好地决定如何合并分支以及如何向文件添加修改，但它并不总是能完全自己做决定。当我们想要合并的两个分支的同一文件中的同一行代码上有不同的修改，或者一个分支删除了一个文件而另一个分支修改了这个文件时，Git 就不知道如何取舍了。

在这样的情况下，Git 会询问你想要保留哪种选择？假设在这两个分支中，我们都编辑了 README.md 的第一行。

![](/images/git/6_2.gif)

如果我们想把 dev 合并到 master，就会出现一个合并冲突：你想要标题是 Hello! 还是 Hey!？

当尝试合并这些分支时，Git 会向你展示冲突出现的位置。我们可以手动移除我们不想保留的修改，保存这些修改，再次添加这个已修改的文件，然后提交这些修改。

![](/images/git/6_3.gif)

完成！尽管合并冲突往往很让人厌烦，但这是合理的：Git 不应该瞎猜我们想要保留哪些修改。




