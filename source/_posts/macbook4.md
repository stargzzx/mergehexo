---
title: MacBook下的z-shell
date: 2019-10-03 20:58:58
categories:
- MacBook
tags:
- MacBook
- z-shell
---
终极shell，你值得拥有，提高效率的神器。

<!-- more -->

# 参考资料

[终极 Shell——ZSH](https://zhuanlan.zhihu.com/p/19556676)
[幕启：介绍 Z shell](https://www.ibm.com/developerworks/cn/linux/shell/z/index.html)
[Mac下切换zsh和bash](https://blog.csdn.net/a19891024/article/details/53869574)

# macbook下的z shell

Shell是Linux/Unix的一个外壳，你理解成衣服也行。它负责外界与Linux内核的交互，接收用户或其他应用程序的命令，然后把这些命令转化成内核能理解的语言，传给内核，内核是真正干活的，干完之后再把结果返回用户或应用程序。

Linux/Unix提供了很多种Shell，为毛要这么多Shell？难道用来炒着吃么？那我问你，你同类型的衣服怎么有那么多件？花色，质地还不一样。写程序比买衣服复杂多了，而且程序员往往负责把复杂的事情搞简单，简单的事情搞复杂。牛程序员看到不爽的Shell，就会自己重新写一套，慢慢形成了一些标准，常用的Shell有这么几种，sh、bash、csh等，想知道你的系统有几种shell，可以通过以下命令查看：

	cat /etc/shells
	
显示如下：

	/bin/bash
	/bin/csh
	/bin/ksh
	/bin/sh
	/bin/tcsh
	/bin/zsh

我的MacBook中默认有 zsh，所以，我就不需要安装了。

目前常用的 Linux 系统和 OS X 系统的默认 Shell 都是 bash，但是真正强大的 Shell 是深藏不露的 zsh， 这货绝对是马车中的跑车，跑车中的飞行车，史称『终极 Shell』，但是由于配置过于复杂，所以初期无人问津，很多人跑过来看看 zsh 的配置指南，什么都不说转身就走了。直到有一天，国外有个穷极无聊的程序员开发出了一个能够让你快速上手的zsh项目，叫做「oh my zsh」，Github 网址是：https://github.com/robbyrussell/oh-my-zsh。这玩意就像「X天叫你学会 C++」系列，可以让你神功速成，而且是真的。

# 安装

这个我就略过了，因为我的电脑上有

# 切换

直接在命令行中运行

	chsh -s /bin/zsh
	
切换回原来的 bash

	chsh -s /bin/bash

# 配置

zsh 的配置主要集中在用户当前目录的.zshrc文件里，用 vim 或你喜欢的其他编辑器打开.zshrc，在最下面会发现这么一行字：
	
	# Customize to your needs…
	
另外，我就没有这个文件，所以，我自己创建了一个，在 ~ 目录下

	touch .zshrc
	
我们要在这个文件中来配置我们的zsh，比如别名设置，在这里，我贴一下别人的别名设置。

	alias cls='clear'
	alias ll='ls -l'
	alias la='ls -a'
	alias vi='vim'
	alias javac="javac -J-Dfile.encoding=utf8"
	alias grep="grep --color=auto"
	alias -s html=mate   # 在命令行直接输入后缀为 html 的文件名，会在 TextMate 中打开
	alias -s rb=mate     # 在命令行直接输入 ruby 文件，会在 TextMate 中打开
	alias -s py=vi       # 在命令行直接输入 python 文件，会用 vim 中打开，以下类似
	alias -s js=vi
	alias -s c=vi
	alias -s java=vi
	alias -s txt=vi
	alias -s gz='tar -xzvf'
	alias -s tgz='tar -xzvf'
	alias -s zip='unzip'
	alias -s bz2='tar -xjvf'
	
zsh 的牛粪之处在于不仅可以设置通用别名，还能针对文件类型设置对应的打开程序，比如：

alias -s html=mate，意思就是你在命令行输入 hello.html，zsh会为你自动打开 TextMat 并读取 hello.html； alias -s gz='tar -xzvf'，表示自动解压后缀为 gz 的压缩包。

总之，只有想不到，木有做不到。
## 配置环境变量

关于MacBook的环境变量你可以参考我下面的文章。

[MacBook的环境变量](https://benpaodewoniu.github.io/2019/10/03/macbook0/)

如果你在 .bash_profile 配置了一些路径，但是，zsh的默认环境中并不会加载这个文件，所以，你在这个文件下配置的路径也就不存在。

如果想要存在，你就得在每一次打开zsh的时候，输入下面的命令来激活 .bash_profile

	source ~/.bash_profile
	
但是，每次都要激活未免太过于繁琐，所以，我们可以在 ,zshrc 文件中加上这个命令。

	source ~/.bash_profile
