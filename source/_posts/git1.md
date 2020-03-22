---
title: git
date: 2019-09-23 21:03:30
categories:
- git
tags:
- git
---
在这一天的上午，用了两个小时的时间学习了 git 的使用。

<!-- more -->

下面这个是我看见过最好的git教程。

[廖雪峰的git教程](https://www.liaoxuefeng.com/wiki/896043488029600)

下面的文章还没看，暂时 mark。

[git 配置多个SSH-Key](https://my.oschina.net/stefanzhlg/blog/529403)

<br/>

# 用法

<br/>

这些用法都是我在币币生息的时候，看看学会的都是经验之谈。
	


## 冲突的解决

如果你执行 git pull 的时候产生冲突，那么你可以先执行

	git stash

然后就是在执行

	git pull
	
再次执行

	git stash pop
	
当我们执行 git stash 的时候，是将自己的本地版本返回到上一个 commit ，也就是你上一次同步版本（你没写代码）的状态，然后 git pull 大概率就会把代码 pull 下来。

然后 git stash pop 是将版本拉到你写的版本，也就是将 commit 指向你的代码版本。

关于冲突一共有两种：

### 冲突1

这种冲突是修改了同一个文件，但是，不修改同一个内容代码，比如：你修改了10-40行，他修改了40-90行，那么，你pull的时候，会产生冲突，但是，当你按照上述的步骤，执行完 pop 后，你们两个的代码相当于合并了，也就是可以继续编写代码了。

### 冲突2

你们修改的是同一处的代码，当你pop后，这个命令也会执行成功，但是，那个文件中会有冲突标志，类似

	<<<<<<< HEAD
	b789
	=======
	b45678910
	>>>>>>> 6853e5ff961e684d3a6c02d4d06183b5ff330dcc

这个时候需要我们自己手动的解决。



## 分支相关

### 分支切换的修改文件问题

git最牛逼的就是分支，但是，有一个就是，当我们正在一个分支上修改，切换分支的时候，怎么隐藏这个分支的内容，出现另一个分支的内容

那就是在分支1中，切换之前先

	git stash

一下，它会把你的修改隐藏

起来，这样切换分支时就不会有问题。在其他分支修改完之后，回来可以通过git stash list看到你的隐藏记录，

并且通过git stash apply stash@{0}这样的方式把修改拿回来。



### git合并某些文件

经常被问到如何从一个分支合并特定的文件到另一个分支。其实，只合并你需要的那些commits，不需要的commits就不合并进去了。

合并某个分支上的单个commit

首先，用git log或sourcetree工具查看一下你想选择哪些commits进行合并，例如：

比如feature 分支上的commit 82ecb31 非常重要，它含有一个bug的修改，或其他人想访问的内容。无论什么原因，你现在只需要将82ecb31 合并到master，而不合并feature上的其他commits，所以我们用git cherry-pick命令来做：

	git checkout master  
	git cherry-pick 82ecb31

这样就好啦。现在82ecb31就被合并到master分支，并在master中添加了commit（作为一个新的commit）。cherry-pick 和merge比较类似，如果git不能合并代码改动（比如遇到合并冲突），git需要你自己来解决冲突并手动添加commit。

这里git cherry-pick每次合并过来会显示文件冲突(其实并没有冲突代码部分，只需手动解决既可)

合并某个分支上的一系列commits

在一些特性情况下，合并单个commit并不够，你需要合并一系列相连的commits。这种情况下就不要选择cherry-pick了，rebase 更适合。还以上例为例，假设你需要合并feature分支的commit76cada ~62ecb3 到master分支。

首先需要基于feature创建一个新的分支，并指明新分支的最后一个commit：

	git checkout featuregit 
	git checkout -b newbranch 62ecb3

然后，rebase这个新分支的commit到master（--ontomaster）。76cada^ 指明你想从哪个特定的commit开始。

	git rebase --ontomaster 76cada^ 

得到的结果就是feature分支的commit 76cada ~62ecb3 都被合并到了master分支。


### git merge 反悔，去掉一些 commit

[参考资料](https://www.zhihu.com/question/325639912/answer/695706958)
