---
title: GIT | 区域知识
date: 2020-03-22 11:46:14
categories:
- git
tags:
- git
---
在这一节，我将介绍 GIT 的工作区、暂存区、版本库、远程仓库的知识。

<!-- more -->

<br/>

# 图片说明

<br/>

![](/images/git/3_0.png)

<br/>

# 工作区

<br/>

工作区就是我们写代码的地方。

当我们修改代码之后，没有做任何 git 操作，那么，我们的所有修改都在 工作区 内。工作区你也可以认为是一个公共的区域。

这个所谓的公共是因为，即便是你在当前分支做了修改，那么你的这些修改也是可以移动到其他分支的。

## 工作区内容移动到其他的分支的例子修改

假设有两个分支

- master
- dev

我们修改了 master 分支的 t.txt 这个文件，如果想要把这份修改转到 dev 可以使用下面的命令

	git switch dev

就可以了。

当然，有些情况下，我们 switch 的时候会出错，所以，我们可以这样

	git stash
	git switch dev
	git stash pop


## 丢弃修改

如果你突然想放弃工作区的修改，你可以使用下面的命令

	git checkout -- filename

我的 git 版本比较新，我可以使用下面的命令

	git restore filenme


<br/>

# 暂存区

<br/>

我们可以将工作区的内容提交到暂存区。暂存区是一个公共的区域，这个区域存放着是将要提交的版本库的代码。

为什么会有暂存区呢？

我们可以想象一下这样一个场景，我们在修改一些文件的时候，不可避免的写多余的代码，比如，打印一下日志等。

当我们同时修改分多文件的时候，假设部分文件已经成熟了，那么我们可以使用

	git add filename

来把那些文件提交到暂存区，而不成熟的文件就可以不提交。同时，暂存区可以多次提交。

比如，我们可以多次 git add 某一个文件。

## 暂存区内容移动到其他的分支的例子修改

假设有两个分支

- master
- dev

我们修改了 master 分支的 t.txt 这个文件，然后，我们使用了

	git add t.txt

如果想要把这个暂存区内容转到 dev 可以使用下面的命令

	git switch dev

就可以了。

当然，有些情况下，我们 switch 的时候会出错，所以，我们可以这样

	git stash
	git switch dev
	git stash pop

## 小技巧

如果，我们的文件非常多，git add 的话，会非常麻烦，那么我们可以使用

	git add .

这样就可以提交所有的工作区修改了。

如果，我们的修改都是在 ant/ dev/ 这两个文件夹内，假设，我们只想提交 ant 里面修改的内容，我们可以在后面直接写目录

	git add ant/


## 从暂存区回归到工作区

有时候，我们 add 完文件之后，后悔了，想要把暂存区的内容再放回工作区，可以使用

	git reset HEAD filename

上面的命令就等同于

	git reset filename

事实上这个完整的命令是

	git reset --mixed filenames

我的 git 版本比较新，我也可以使用下面的命令达到效果

	git restore --staged filename

<br/>

# 版本库

<br/>

版本库最好应该称为特定分支的版本库。即，每一个分支都会有自己的版本库。使用

	git commit -m 'info'

假设我们在 master 分支，那么运行这个，就推送到 master 的版本库，如果在 dev 分支，那么运行这个，就推送到 dev 的版本库。

版本库是特定的不是公共的。

我们每次 commit 会得到一个 十六进制 的版本号。

假设，我们后悔 commit 了某一个版本，可以使用下面的命令将版本库的修改回退到暂存区。

	git reset --soft HEAD^

在Git中，用HEAD表示当前版本，也就是最新的提交1094adb...（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD\~100。

这个特地说一下 git reset 的三种模式。

- \-\-soft
- \-\-mixed
- \-\-hard

## \-\-soft

保留源码,只回退到 commit 信息到某个版本.不涉及暂存区 （index） 的回退。

假设我们修改了 t.txt

并且执行了

	git add .
	git commit -m 'info'

我们如果执行

	git reset --soft HEAD~1

我们使用

	git status

显示

	On branch test
	Changes to be committed:
	  (use "git restore --staged <file>..." to unstage)
		modified:   t.txt

这表明 git reset \-\-soft 是将对 t.txt 的修改回退到暂存区。

## \-\-mixed

这个是 git reset 的默认模式。

会保留源码,只是将 git commit 和 index 信息回退到了工作区。

还是上面的情景，执行

	git reset --mixed HEAD~1

然后使用 git status

	On branch test
	Changes not staged for commit:
	  (use "git add <file>..." to update what will be committed)
	  (use "git restore <file>..." to discard changes in working directory)
		modified:   t.txt

说明这个将 commit 的东西回退到了工作区。

## \-\-soft

回退到commit 信息到某个版本。

这个回退的比较彻底，是版本回退，不是 commit 撤销回退。

比如，我们现在有

	latest:65231q...
	7365a3...

这两个版本，假设，我们现在最新版使用

	git reset --soft HEAD~1

那么，我们现在的版本就是 7365a3，在 7365a3 之后的修改都会不见。

<br/>

# 远程仓库

<br/>

## 推送到远程仓库

	git push origin master

## 线上代码拉到工作区

	git pull origin master