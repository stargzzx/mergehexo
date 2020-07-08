---
title: macbook安装brew及用法
date: 2019-10-09 21:18:25
categories:
- MacBook
- brew
tags:
- MacBook
- brew
- git
---
brew是MacBook的一个安装软件的工具，非常好用，每个人MacBook电脑都必备。

<!-- more -->

<br/>

# 参考资料

<br/>

[官网](https://brew.sh/index_zh-cn)
[Mac下安装homebrew下的error：RPC错误](https://blog.csdn.net/qq_38206417/article/details/88568773)

<br/>

# 安装的过程

<br/>

如果你直接使用官网的安装命令

	/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

那么你可能会报这个错误

	error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL
	
经过查资料貌似是因为你的MacBook太新了。。。

所以，直接上解决方案：

## 将brew的install文件下载本地

	curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install >> brew_install
	
## 修改install文件的镜像源

（进入访达用command+shift+G找到brew_install文件进行修改，之间点击文件，MacBook会有默认的软件打开【至少我的是这样】）

将

	BREW_REPO="https://github.com/Homebrew/brew".freeze
	
改为

	BREW_REPO="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git".freeze

我的install文件里没有大佬说的CORE_TAP_REPO

所以我在BREW_REPO下填加

	CORE_TAP_REPO="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git".freeze
	
ps：我的文件有 BREW_REPO 。。。

(查看了很多帮助，个人觉得有的po主用git://代替https://不会成功，害怕电脑连接不上，我直接试的就是https://）

![](/images/macbook/5_0.png)

## 开始安装

	/usr/bin/ruby brew_install
	
看到如下说明安装成功

![](/images/macbook/5_1.png)

。。。

![](/images/macbook/5_2.png)

ps：安装过程缺失和图中差不多

成功啦（然而并没有结束）

## 替换homebrew源

	cd "$(brew --repo)” 
	git remote set-url origin https://mirrors.ustc.edu.cn/brew.git
	cd "$(brew --repo)/Library/Taps/homebrew/homebrew-core” 
	git remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git
	
## 更新

	brew update
	
## 设置 bintray镜像

	echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
	source ~/.bash_profile
	
按照教程，我的到这一步就成功了

## 仍然安装失败

如果还不成功，可能因为我之前试了以下命令（我觉得不太可能）：

	git config --global http.postBuffer 524288000
	
这个命令运行之后要关闭终端再重新打开一个，再：

	/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

另外说一说为什么会执行

	git config --global http.postBuffer 524288000
	
这个主要是因为：

[mac 安装brew遇到错误](https://my.oschina.net/happyfish319/blog/3089195)

<br/>

# Git error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 54

<br/>

错误信息：

	Total 116323 (delta 81906), reused 116320 (delta 81905)
	POST git-receive-pack (130907163 bytes)
	error: RPC failed; curl 56 LibreSSL SSL_read: SSL_ERROR_SYSCALL, errno 54
	fatal: The remote end hung up unexpectedly
	fatal: The remote end hung up unexpectedly
	Everything up-to-date
	
git 在 pull 或者 push 一个大项目时，可能会碰到出现如题所示的错误。

解决办法：

网络上往往都会说这是由于大文件造成的提交或者拉取失败。但是，经过本人测试，如果是 errno 56，那么应该是有大文件或者提交缓存方面的问题。而 errno 54 则不是这个问题。对于 56 错误的解决方式与网络上大部分文章的一致。都是增大缓存配置，比如下面就是配置提交缓存为 500M。

	> git config --global http.postBuffer 524288000
	> git config --global https.postBuffer 524288000
	
对于 errno 54 这个错误，经尝试 http 或者 https 协议都无法正常提交。必须改为 ssh 方式来提交代码。也就是必须使用公私钥的方式进行账号验证，并提交代码。

<br/>

# brew 的用法

<br/>

	brew search **  //查找某个软件包
	brew list  //列出已经安装的软件的包
	brew install ** //安装某个软件包,默认安装的是稳定版本
	brew uninstall **//卸载某个软件的包
	brew upgrade ** //更新某个软件包
	brew info ** //查看指定软件包的说明
	brew cache clean //清理缓存

