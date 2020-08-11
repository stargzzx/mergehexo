---
title: gitbook | 上传到 github
date: 2020-08-11 16:43:41
categories:
- gitbook
tags:
- gitbook
---
如何将自己的电子书上传到 github.

<!-- more -->

<br/>

# GitHub 中建立对应仓库

<br/>

假设我在 `github` 中创建了一个仓库，叫做 `blockchain`。


<br/>

# 电子书上传

<br/>

假设我的 `gitbook` 项目叫做 `blockchain`。

此时，我已经使用

	gitbook init

进行了生成，然后，我是用

	git remote add origin git@hexo.com:benpaodewoniu/blockchain.git

- 这里为啥是 `hexo.com` 而不是 `github.com`，你可以参考我下面的博文
	- [在同一电脑下建立多个 ssh key](https://benpaodewoniu.github.io/2020/01/04/ssh0/)

`master` 分支用于存放源代码。

	git add .
	git commit -m 'update'
	git push -u origin master

然后我们创建一个分支，叫做 `gh-pages`。

- 必须是 `gh-pages` 有了这个分支，Github会自动为你分配一个网址。
- `master` 分支用于放源码，`gh-pages` 用于放产生的页面

<br/>

	git branch gh-pages 「我的 git 比较新，有的人需要使用 `checkout`」
	git switch gh-pages
	rm -rf *
	git add .
	git commit -m 'update'
	git push origin gh-pages

上面的那些步骤主要是给远程仓库建立 `gh-pages` 分支。

我们找一个其他的目录，比如我是

	~/bookweb

然后，我进入这个目录，执行

	git clone -b gh-pages git@hexo.com:benpaodewoniu/blockchain.git blockchain

就可以看到下面的出现了一个目录 `blockchain`。

然后返回 `blockchain` 的源码。执行

	gitbook build

会看到生成一个叫做 `_book` 的文件夹，里面存放着生成出来的文件，诸如 `html` 等。

然后，我们将生成出来的东西，放到 `~/bookweb/blockchain` 里面。

也就是

	mv ./_book/*  ~/bookweb/blockchain
		需要注意的是，是 _book 的内容放到 文件夹中，而不是 _book 放到文件夹

值得注意的是，在 `mv` 之前要把原来的内容删除，但是不能把 `.git` 给删了。

	rm -rf `ls`
		使用该命令删除

然后，进入 `~/bookweb/blockchain` 将内容上传到 `github` 的 `gh-pages` 分支中。

进入 `github` 的 `blockchain` 项目。找到 `setting`

![](/images/gitbook/1_0.png)

使用 `gh-pages` 作为项目的主页。


<br/>

# 注意点

<br/>

## 注意点一

很多人都觉得我上面为啥要 `mv`，而不是直接在 `~/bookweb/blockchain` 中生成，比如

	gitbook build ./ ~/bookweb/blockchian

我尝试过这样，但是，生成的时候失败了，也不知道是咋回事。

## 注意点二

在 `master` 分支的那个目录下生成 `.gitignore` 文件。

忽略_book 下的静态网页文件