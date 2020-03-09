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

## 上传

当你写完代码之后首先第一步是先同步同事的代码，也就是从远程仓库中将最新的版本库下载下来。

使用的命令是

	git pull
	
当你把同步完成后，需要运行的是下面的这个命令

	git status
	
这个命令式检查你的本地代码的状态，是一个非常重要的命令，你的每一步执行完成后都可以执行这个命令，它可以让你知道，你的哪些文件修改了，你的工作区是什么状态。总之，是一个非常重要的命令，后面我就默认在执行完每一条命令后，我都执行这个命令了。

当我们通过上面的命令，查看到我们改的文件后，下一步就是将想要上传的文件进行添加。

采用下面的命令

	git add ***
	
这个 *** 代表的是文件名，另外，这个 add 命令是添加一个文件，如果你想添加多个文件，就需要多次执行这个命令了。

当添加完成后，我们需要 commit 一下，也就是给我们的修改做一个备注，其具体的命令如下：

	git commit -m "里面是你想要备注的信息"
	
通常，建议这个备注的信息都是要有明确含义的。

最后，就是将我们的本地代码提交到远程仓库里面，采用下面的命令。

	git push origin master
	
## 什么时候上传

这件事我和陈哥讨论过，究竟什么时候将本地代码 push 上去，陈哥给的建议是，只要有一点有效的修改，就要提交。关于这点陈哥的解释如下：如果时间拖得过长，那么版本库就会变得越来越大，而你的本地代码就会和版本库差别的越来越大，可能就会导致冲突，或者目录的变更，对维护产生很大的影响。

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

## 紧急BUG修复

当你在写代码的时候，突然，线上出现了一个紧急的BUG，但是，你又不能停下你手中的工作，那么你可以执行下面的命令

	git stash

让 commit 指向你的上一个commit，这样的话，你可以直接先修改BUG，然后提交，提交的是你修改BUG的代码，当你，修改完成后，可以再次执行

	git stash pop
	
这样，你又回到了之前的工作状态。

### 切换到别的分支，保存当前分支修改方法

承接上面

	git stash save “修改的信息"
	
这样以后你的代码就回到自己上一个commit了，直接git stash的话git stash的栈会直接给你一个hash值作为版本的说明，如果用git stash save “修改的信息”，git stash的栈会把你填写的“修改的信息”作为版本的说明。

接下来你回到old分支修改代码完成，你又再回到new分支，输入：

	git stash pop

或者

	git stash list
	git stash apply stash@{0}

就可以回到保存的版本了。git stash pop的作用是将git stash栈中最后一个版本取出来，git stash apply stash@{0}的作用是可以指定栈中的一个版本，通过git stash list可以看到所有的版本信息：

	stash@{0}: On order-master-bugfix: 22222
	stash@{1}: On order-master-bugfix: 22222
	
	然后你可以选择一个你需要的版本执行：

	git stash apply stash@{0}

这时候你搁置的代码就回来了。

当然，你也可以用

	git stash pop stash@{int}

这两者的区别是：

>git stash pop stash@{id}命令会在执行后将对应的stash id 从stash list里删除，而 git stash apply stash@{id} 命令则会继续保存stash id。

### 一个小小的告知

我一开始难以理解git的作用，因为我用的是pycharm，所以，无论是我怎么git stash ，pycharm 上并不会隐藏我刚刚写的代码，所以，我就产生了一个疑问，如果pycharm不会隐藏代码，那么我提交的时候不就提交了全部代码吗？

这个是错误的，pycharm是ide工具，只要你对文件进行改变，那么肯定会直接在窗口上进行展现，但是，我们的提交方法确是受到git的影响。

我们的git会记录，你在每一个版本修改的内容，修改了哪些东西，当你提交的时候，只是提交了git指向的那个 commit 而已。

git的提交并不是将文件进行提交，而是，它自己有一套自己的记录方式，所以，git才会被我们称为版本控制器。

### 多次 stash 忘记如何返回

这个情况我还没并见过，但是，还是有可能碰见的，所以，先记录一下

[git stash和git stash pop](https://blog.csdn.net/wh_19910525/article/details/7784901)

git stash 可用来暂存当前正在进行的工作， 比如想pull 最新代码， 又不想加新commit， 或者另外一种情况，为了fix 一个紧急的bug,  先stash, 使返回到自己上一个commit, 改完bug之后再stash pop, 继续原来的工作。

当你多次使用’git stash’命令后，你的栈里将充满了未提交的代码，这时候你会对将哪个版本应用回来有些困惑，

	git stash list
	
命令可以将当前的Git栈信息打印出来，你只需要将找到对应的版本号，例如使用

	git stash apply stash@{1}
	
就可以将你指定版本号为stash@{1}的工作取出来，当你将所有的栈都应用回来的时候，可以使用

	git stash clear
	
来将栈清空。

![](/images/bbsx/2_0.png)

那么，当我们想要清除某一个 stash 的时候，使用下面的命令：

	git stash drop <某一个标示>
		git stash drop stash@{1}

如果不填写标示，默认删除最新的。

## 查看之前的版本内容

我们使用 git log 命令来查看过往版本的内容。

![](/images/bbsx/2_1.png)

然后，我们复制想要看的版本的 commit，然后执行

	git show ***
	
![](/images/bbsx/2_2.png)

## 使用线上版本强制覆盖本地版本

	git fetch --all
	git reset --hard origin/master

## 多个文件上传

由于我有承包了 eosvoter 这个项目的部分前端工作，所以，前端也需要使用文件上传到 git。

但是，有经验的小伙伴都知道，前端修改页面一次性会修改很多，所以一个个的使用 git add filename 添加不现实，所以，我们只需要一次性添加

我们可以使用

	git add .
	
这个默认将我们所有的文件添加到工作区，但是，有时候，有些文件，我们并不希望上传，所以，我们要撤回一些文件，所以，我们使用命令：

	git reset HEAD filename
	
如果我们直接使用下面的命令

	git reset HEAD
	
就会把所有 add 的文件全部撤回来。

## 忽略文件

如果，我们再上一步回撤的文件过多，未免工作量太多，并且，我们需要有些文件，是需要自动忽略，不上传的，比如log 文件，本地配置文件等。

有时候我们提交自己建的项目中想忽略某些文件夹可以建一个.gitignore目录，里面指定想要忽略的文件或目录，比如自己eclipse的项目可以设置忽略bin/和gen/目录，这样就表示把 bin 目录和 gen 目录下的所有文件都忽略掉,从而使用得它们不会加入到版本控制当中

如下图所示：

![](/images/bbsx/2_3.jpg)

## 撤销文件与回退到某一版本

我因为一些原因需要在服务器上进行一些文件修改，但是，修改完之后，我在执行

	git pull
	
是显示错误，这是因为我没有把修改的内容上传，当然，由于某些原因，我在服务器上也不能执行

	git stash
	
但是，即便是执行了这个命令还是会造成隐患，最好的方法就是将修改的文件内容回退到之前的状态，这个时候，我们可以使用下面的命令

	git checkout /Users/dxy/GitJsSdk/js-sdk/README.md
	
checkout 后面跟着具体的文件名，可以让当前版本修改的内容回退到之前的版本，但是，这个命令只适用于那些还没有执行过 add 命令的文件,如果某个文件已经被添 加过了,这种方式就无法撤销其更改的内容。

所以，对于已经 add 的文件，我们要撤回，也就是执行

	git reset HEAD fielname

## 强制回退到上一个版本

	git reset --hard HEAD^

## 版本回退

如果一次性改变的文件太多了，想把版本直接进行回退，可以使用

	git reset --hard 版本号

注意：这个命令是不会保留指定的那个版本到现在本地的修改的，去掉–hard参数，就可以保留修改，执行完之后可以git status看到。

### 工作区回复

	git checkout .
	
实际就是你还原了index这个文件 你的修改都恢复了 相当于你没改动过这个文件

### 仅仅回退某一个具体的文件

	git checkout [<options>] [<branch>] -- <file>...

那么执行的时候就是这个样子：

	git checkout  ee25a1a3f9465 config/database.php

如果要回退整个文件夹到某个版本呢？

	git checkout e2fb85c827d24a460d app/Http/Controllers/Auth

#### 某一个具体的文件

	git log

查看 commit 的头信息

	git checkout 某一个版本号 具体文件名

#### 某一个文件夹

	git checkout 某一个版本号 具体目录

## 分支相关

### 查看所有分支

	git branch -a

### 创建分支

	git branch 分支名字

有一点需要注意的是，如果没有特殊情况，我们的创建分支应该是通过 master 来创造，并且，要保证 master 的代码是最新版。

### 切换分支

	git checkout 分支名字
	
### 创建分支和切换分支

	git checkout -b 分支名字
		这个命令相当于上面两个命令的结合

### 分支切换的修改文件问题

git最牛逼的就是分支，但是，有一个就是，当我们正在一个分支上修改，切换分支的时候，怎么隐藏这个分支的内容，出现另一个分支的内容

那就是在分支1中，切换之前先

	git stash

一下，它会把你的修改隐藏

起来，这样切换分支时就不会有问题。在其他分支修改完之后，回来可以通过git stash list看到你的隐藏记录，

并且通过git stash apply stash@{0}这样的方式把修改拿回来。

### 分支合并

我们切换到自己创建的分支，比如 bitmex 。然后，我们想把 master 的代码合并到 我们创建的分支上。

所以，我要先切换到 master，然后 pull ，保证 master 的代码是最新的。

然后，在切换到 bitmex 中，执行 git merge master

这个 merge 操作，是你在哪个分支，就会自动吧 merge 后面的分支和当前分支的代码进行合并。

当然，有的时候，我们手头的工作还没有完成就需要开启分支做其他的，这时有两个选择，第一，就是通过 add commit push 把手头的工作提交到远程，但是，有的时候你的工作并不能运行，所以，你不能提交，要知道，一个良好的习惯，你提交的代码必须得是可运行的，另一种方法就是，我们先 stash ，然后，修改完成后，我们再 pop 回来

### Git合并指定文件到另一个分支

如果只想将feature分支的某个文件f.txt合并到master分支上

	git checkout feature 
	git checkout --patch master f.txt

第一个命令： 切换到feature分支；

第二个命令：合并master分支上f文件到feature分支上，将master分支上 f 文件追加补丁到feature分支上 f文件。你可以接受或者拒绝补丁内容。

如果只是简单的将feature分支的文件f.txt copy到master分支上；

	git checkout master
	git checkout feature f.txt

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

### git merge 分支实操

团队在写项目代码的时候，经常会建设多个分支。但是，最终，我们都是要回归 master 分支的。

在这里，我们有一个情况，就是，当团队成员将 master 分支代码修改之后，而你正在 update_li 这个分支上修改，所以，你虽然一开始是基于 master 创建的分支，但是，你现在的代码已经和 master 有了很大的不同。

这里，假设一种情况。如果，在你的分支上，你已经测试过无误之后，首先你将代码更新到 update_li 这个分支上，这个时候，你需要将你的代码合并到 master 上。以下是具体的步骤。

首先，要合并 master ，我们的原则是 master 的代码是可以稳定运行的代码，所以，冲突方面应该在自己的分支上解决。

我们先切换到 master 分支上，然后 git pull origin master ，我们将 master 的代码变成最新的代码。

然后，我们切换到 update_li 这个分支上，我们使用

	git merge origin master
		这个 origin 是指远程仓库，这句话的意思是 merge 远程仓库的 master
	由于之前我们已经将本地的 master pull 到最新的代码
	所以，直接用
	git merge master（merge 本地代码）
	这条命令也可以，当然最好还是加上 origin

我们在 update_li 上解决完冲突后，使用 git status 看一下

这个时候，你会发现，有很多 文件需要我们 commit ，这是很正常的事，因为，你从 master 分支上，merge 了很多文件。这些文件都是你之前没有的，而且，你 merge 的话，就默认，这些文件属于你的，你只需要把这些文件提交到远程分支上就好了。

ok，现在我们就默认你已经将代码提交到 update_li 上了，在这里我需要重申一遍，update_li 上面保存的代码应该是 最新的 master + 你自己写的代码。

现在让我们切换到 master 上。

我们执行

	git merge origin update_li

这样，在 master 上，我们的代码就是已经处理完冲突之后的代码了。

这个时候，你再讲代码 git push origin master 就好了。

### git merge 反悔，去掉一些 commit

[参考资料](https://www.zhihu.com/question/325639912/answer/695706958)
