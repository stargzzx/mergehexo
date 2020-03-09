---
title: 多个电脑协同更新hexo
date: 2019-10-07 13:56:38
categories:
- hexo
tags:
- hexo
---
这个其实是很需要的，但是，目前，我对这个需求不算是特别大，但是，先mark起来，等有需要的时候再来。

<!--more-->

<br/>

# 参考资料

<br/>

[Hexo的版本控制与持续集成](https://formulahendry.github.io/2016/12/04/hexo-ci/)
[使用hexo，如果换了电脑怎么更新博客？](https://www.zhihu.com/question/21193762)

<br/>

# 总结如下

<br/>

<br/>

# 原电脑上传文件

<br/>

hexo 是一个静态博客，我们在 source 文件夹中写文章，然后运行 hexo g 来生成静态文件，也就是 public 文件夹，然后 hexo d 将 public 文件夹上传到 github 上。

所以，如果，我们能够对 source 文件夹及其其他相关文件做一个同步，我们就可以多终端编写博客。在这里，我们利用的是 git 的版本控制功能。我们只需要在 blog 中创建一个 git 分支，然后不同的终端同步就好了。

首先，我们先在原 blog 中，创建一个叫做 .gitignore 文件，然后在该文件中填写

	deploy_git/
	public/

这一步是为了告诉 git 要忽略这两个文件。

## 初始化仓库

在博客根目录下，依次执行

	git init

	git remote add origin 为远程仓库地址
		我的是 git remote add origin git@github.com:benpaodewoniu/mergehexo.git
			ps：mergehexo 这个仓库，我是去 github 建完后才执行的这个


注意上面的 remote 后面有一个 .git ，你在创建仓库的时候，会跳出一个新的界面给你说明，如何关联，下面我创建了一个新的 test 仓库，图片如下

![](/images/hexo/9_0.png)

## 同步到远程仓库

	git add . #添加目录下所有文件
	git commit -m “更新说明” #提交并添加更新说明
	git push -u origin master #推送更新到远程仓库
		在这里要尤为注意 是有一个 -u 的，新电脑上传的时候一定要加上，不然会出一些问题

ps:在这里可能会遇到上传不成功，是因为我们并没有给仓库添加 ssh key。

具体你可以参考一下我的博文

# 新电脑拉下仓库

电脑上同样先安装好node、git、ssh、hexo，然后建好hexo文件夹
在项目目录下安装好插件

	npm install hexo-deployer-git
            安装这个是为了使用 hexo new 这个命令

你可以搜索关于如何安装上面的东西，在这里我们不再过多的描述

## 拉下仓库

	git init 
	git remote add origin <server> 
		git remote add origin git@github.com:benpaodewoniu/mergehexo.git
	git fetch --all 
	git reset --hard origin/master

<br/>

# 发布文件

<br/>

在这里我们新建文件写完后要把新的文件 pull 上去

	git add #可以用git master 查看更改内容  
	git commit -m "更新信息"  
	git push -u origin master #以后每次提交可以直接git push

<br/>

# 同步

<br/>

每次写文件首先要 git pull 一下

或者运行下面

    git pull origin master

<br/>

# 错误

<br/>

这整个过程中，主要遇到两个错误

## 问题1

[hexo本地测试运行重启后页面空白,提示 : WARN No layout: index.html?](https://www.zhihu.com/question/38781463)

新电脑无法 hexo s，提示没有 index.html，因为这个原因，你 hexo d 之后，导致整个网站都是空。

经过查询相关的问题，得知，是 themes 下的主题为空的原因，去看了一下发现我的 next 文件夹确实为空，所以，我从原电脑中复制了一份放到相应的目录下，最后，成功。

## 问题2

hexo 部署的时候 停在了一半不动了：

	On branch master
	nothing to commit, working directory clean

[参考资料](https://www.zhihu.com/question/44219609)
[Hexo deploy 发布不成功](https://github.com/hexojs/hexo/issues/67)

我用新的电脑 hexo d 之后，导致，旧电脑不能够再 hexo d ，经过查询要用下面的步骤才行

网上的解决方案

	rm -rf .deploy
	hexo generater
	hexo deploy

因为我的是windows的，所以，我手动删除了 .deploy_git 这个文件，win 下没有 rm 命令。

然后用上面的命令成功。我认为其是如下的原因，我们都知道 git 之所以是版本控制是因为有 .git 这个文件记录。

而hexo 的控制就是 .deploy_git 这个文件夹的内容，并且这个文件夹是本地生成的，也就是 hexo generater 或者 hexo g 生成的。

我们在某一个电脑上编辑后，生成的 .deploy_git 肯定是新的，如果换了一台电脑肯定是不行的，所以，我们最终删除本地的，然后 git pull 下来，让所有机器的源文件保持一致

然后再生成，自然也就能 hexo d 上去了，所以，我们以后用 hexo 的时候，要用一台电脑执行。

## 问题3

出现 LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443

这是因为我的新电脑上用了 ShadowsocksX-NG 代理，我关了之后就好了。



