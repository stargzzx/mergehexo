---
title: git 版本控制
date: 2018-06-13 14:39:51
categories:
- git
tags:
- git
- github
- tutorial
---
这里介绍关于 git 方面的知识。

<!--more-->

## 初始

### 初始

{% codeblock %}
安装完 GIT 后，首先要进行配置
打开 cmd
输入 git config --global user.name "用户名"
     git config --global user.email "邮箱"  
			注意不要用中文 

	git config --global user.name "licong"
        git config --global user.email "1506316219@qq.com"
输入完成是没有任何显示的
需要输入 git config --list 进行检验
{% endcodeblock %}

### 理论基础 

{% codeblock %}
svn 记录的是每一次版本的变动内容
git 则是每个版本的独立存储
优秀的程序员更侧重于程序设计

git的工作流程：
	1.在工作目录中添加，修改文件
	2.将需要进行版本管理的文件放在暂存区域
	3.将暂存区域的文件提交到 git 仓库

git管理的文件有三种状态
	已修改（modified）
	已暂存（staged）
	已提交（commited）
{% endcodeblock %}

### 建立项目

{% codeblock %}
建立一个新的文件夹
在此文件夹下按住shift 右键在此处打开命令行
输入 git init 即创建
创建一个 README.md 文件                 所有的文件的编码都应该是utf 8 无 bom格式
输入 git add README.md                  加入暂存区域,这样 git 就可以追踪这份文件
     如果想反悔，不想让 git 追踪可以输入 git rest HEAD 将仓库恢复到先前状态。也可以在后面指定文件名 git rest HEAD 文件名  
     git commit -m "add a readme file"  将暂存区域提交到仓库
git add * 提交所有文件
{% endcodeblock %}

## 查看工作状态和历史提交

{% codeblock %}
git status 查看状态 显示的是暂存区域的内容，比如修改的文件，未提交的文件
提交过后 git status 不会有文件的显示
创建一个文件名为 LICENSE 无扩展名
	这个文件是说明此项目可能的协议，如MIT
	MIT协议是所有开源许可中最宽松的一个

如果对已有文件进行改变，它的所属状态也是在暂存区域，用 git status 可以查出

	但是如果对所改内容并不满意，想要返回原来的状态
	可以调用 git checkout -- 文件名 （--和文件名之间有空格）
		它的作用是把暂存区域的内容给删除

事实上，git的使用就是三个文件夹之间的状态转换，即创建文件，暂存区，存储区（提交后）

	创建一个文件
	利用 git add 文件名 让该文件加入暂存区
			    加入暂存区的文件可以用 git status 进行查询
	最后提交这个文件 即 git commit -m "add a readme file"  将暂存区域提交到仓库

	假如在后续操作中，继续修改某一已经存在的文件
		调用 git status 可以知道修改的内容并没有到暂存区
		所以继续调用 git add 文件名
		再次调用 git status 可以发现，文件已经进入暂存区
		
有这样一种情况

	对已有文件进行添加后，记起为状态A，再调用git add 文件名后
	再次对此文件进行添加，记起为状态B
	调用 git status 可以发现，出现了两种状态，即已经进入暂存区的，没有进入暂存区的
	分析可知，已经进入暂存区的是状态A，没有进入暂存区的是状态B
	所以，接下来如果调用 git commit -m"描述信息" 存储的是状态A
	如果调用 git add 文件名，存储的是状态B

git log 查看历史提交
git log --decorate 查看所有，包括分支等
git log --oneline 精简化形式显示
git log --decorate --oneline --graph --all 以图形化显示所有分支
{% endcodeblock %}

{% img /images/git/0_0.JPG %}

## 回到过去

{% codeblock %}
git reset HEAD~ 返回上一个版本
	返回上一个版本后，所变化的文件状态会回到暂存区域
	而 ~ 的个数代表的是返回上几个版本
	如 git reset ~~~ 是返回上次第三个版本
	也可以写成 git reset ~5 表示返回上次第五个版本

reset的参数

	git reset --mixed HEAD~ 默认为这个
		移动HEAD的指向，将其指向上一个版本
		将HEAD移动后所指向的版本，回滚到暂存区域

	git reset --soft HEAD~
		移动HEAD的指向，将其指向上一次版本
		这项功能是防止误提交

	git reset --hard HEAD~
		移动HEAD的指向，将其指向上一个版本
		将HEAD移动后指向的版本回滚到暂存区域
		将暂存区域的文件还原到工作目录
		为危险操作，相当于直接回到上一版本
			即相当于git reset --mixed HEAD~ 
				git commit -m"描述信息"

回滚到指定版本
	不需要写全部的ID号，只需要用前五位即可
		git reset ID号（前几位）

回滚到个别文件
	git reset 版本号 文件名/路径

当然 reset也可以回到未来，即以前修改的但是又放弃，后来又想回去
	条件是必须知道之前的ID号
{% endcodeblock %}

{% img /images/git/0_1.png %}

## 版本对比

{% codeblock %}
git diff
	出现两个版本不同的地方

比较两个历史版本
	git diff 版本ID1 版本ID2

比较暂存区域和GIT仓库的版本
	git diff --cached  在后加上ID后，指定某一版本和暂存区的版本
{% endcodeblock %}

## 修改最后一次提交，删除文件和重命名

{% codeblock %}
git commit --amend 
	更正上一次操作
		比如提交完一次版本后，发现有两个文件没有添加，又不值得为这两个
		文件添加新版本，使用这个命令可以更正
		想修改文件，又不想使用reset，可以使用此文件
	使用此命令，会进入一个编辑界面
		按下 i 左下角会出现 INSERT
		按下ESC
		输入:q! 是退出，不修改

删除文件
	删除后，如果想恢复则
	可以调用 git checkout -- 文件名 （--和文件名之间有空格）

提交错文件后
	git rm 文件名  
		该命令删除的只是工作目录和暂存区域的文件，也就是取消跟踪，在下次
		提交时不纳入版本管理。
		所以，会出现所在版本出现不同，因为一个文件不见了，所以可以提交
		也可以回滚。

重命名
	如果自己直接修改，对git来说是两处改变
		1.原来的文件删除
		2.添加了一个新的文件
	所以直接用命令
		git mv 旧文件名 新文件名
{% endcodeblock %}

## 分支

{% codeblock %}
HEAD 是一个指针，它指向的是现在正在使用的分支
默认为master
创建分支
	git branch 分支名

切换分支
	git checkout 分支名
		将 HEAD 指向这个分支
	git checkout -b 分支名
		新创建一个分支，并使 HEAD 指向它

如果改变文件，只是改变的 HEAD 指向的分支的内容
	如现在有两个分支，一个为master另一个为second
	并且各有两个相同迭代版本
	假设切换分支到second，对文件进行改变，提交后
	second变成了三个版本
	切换到master分支，调用git log
	发现里面只有两个版本，且文件没有改变
{% endcodeblock %}

## 合并分支和删除分支

{% codeblock %}
创建分支
git merge 分支名
	将指定分支合并到HEAD指向的分支
	但这个命令可能会出现冲突，比如两个分支中，都对同一文件进行修改，所以最后
	git不知道该怎么处理，所以git会在文件中加上标志
	我们需要自己打开文件进行修改
	修改会调用
		git add 文件名
		git commit -m""

删除分支
	git branch -d 分支名
{% endcodeblock %}

{% img /images/git/0_2.png %}

## 匿名分支

{% codeblock %}
如果checkout后面没有跟ID而是
		git checkout HEAD~

假设只有一个分支 master
执行上面的命令后，HEAD会指向上一个版本，但是没有分支名，所以这是匿名分支
但是匿名分支由于没有名字，在checkout别的分之后，对匿名分支的改变就会消失，
所以匿名分支也可以用于实验某些功能。
在匿名分支下，checkout别的分支，匿名分支修改的内容就会消失，所以也可以调用
	git branch 分支名 ID号
	来为匿名分支命名

checkout有两种功能
	1.切换分支
	2.将暂存区域中的文件拷贝到工作目录
{% endcodeblock %}

