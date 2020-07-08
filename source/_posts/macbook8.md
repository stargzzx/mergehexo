---
title: MacBook | brew 本地安装
date: 2020-02-10 00:04:48
categories:
- MacBook
- brew
tags:
- MacBook
- brew
- brew cask
---
有的时候我们使用 brew 安装的时候很慢，这个时候，我们的需求就变成了先下载到本地然后再安装。

<!-- more -->

想要知道如何用 brew 进行本地安装，首先要知道 brew 的缓存目录。

所谓的缓存目录，就在本地存储从网上下载下来的包，我们只需要将包放在哪里就好了。

所以，我们第一步是知道缓存目录

使用

	brew --cache

我的是

	/Users/licong/Library/Caches/Homebrew

在我这个目录下存在已经下好的包，也存在一个文件夹是 downloads 这个包里面存放着一些正在下载的文件。

如果是还没有下载完，其后缀名是

	incomplete （brew cask 安装方式）

我举的例子是 jdk8 ，因为我想安装 kafka ，所以，在安装的过程中出现了他们叫我先安装 jdk8 ，即执行下面的命令：

	brew cask install homebrew/cask-versions/adoptopenjdk8

但是，我本地下载非常的慢，使用迅雷下载（资源的连接，当你使用 brew 安装的时候，相关资源的连接一起出现在命令行界面）也非常的慢，但是好歹下下来了。

在这里，我必须说明一下 brew 和 brew cask 的区别：


brew主要用来下载一些不带界面的命令行下的工具和第三方库来进行二次开发。brew cask主要用来下载一些带界面的应用软件，下载好后会自动安装，并能在mac中直接运行使用举个例子，

	brew install curl可以安装curl第三方库，这样你在开发时就可以使用它的库来进行开发。
	brew cask install chrome可以安装谷歌浏览器应用程序，可直接运行brew偏管理第三方库和命令行工具方面的东东。

brew cask可以看作是苹果官方app store的补充。

是一个众多贡献者们维护的非苹果官方软件商店，你也可以在这里下mac软件用一些免费好用的mac软件没有在苹果官方app store商店上架，我们就可以在brew cask中下载。如果我要下载10个免费小软件，而这些软件没有在苹果商店上架，我们不需要一个一个去谷歌它们的官方网站，再去这些软件的官网去下载，我们统一在brew cask中下载。使用brew cask来进行包管理还有一个好处，这10个免费软件如果自身不带升级功能，但现在它们有更新，我只能去官网重新下载？不，直接在brew cask里就可以统一升级。这也是你问的那句“为何网路上跟推荐用brew cask呢？”的原因。

如果安装mac图形界面软件，推荐先在苹果官方商店里搜索下载，没有的话去brew cask试试，如果还没有，只能去这个软件的官方网站去下载了。

这里由于采用的是 brew cask ，所以，这个也只是针对这个方式的本地安装，不保证 brew 安装方式一样。而且，我的安装方式和网络上流传的不太一样。不知道是不是我电脑版本的问题。

我的电脑是

	ProductName:	Mac OS X
	ProductVersion:	10.14.5
	BuildVersion:	18F132

<br/>

# 网上流传的 brew 的本地安装例子

<br/>

[mac中使用brew安装软件，下载太慢怎么办？](https://www.jianshu.com/p/03d1db713f76)

<br/>

# brew cask 的本地安装

<br/>

经过我的探索，我的 brew 的缓存目录是

	/Users/licong/Library/Caches/Homebrew

而，我的 brew cask 的缓存目录在

	/Users/licong/Library/Caches/Homebrew/downloads/

并且，我在网络上，看到，有时候即便是我们把文件正确的放在目录下，也有可能不能安装，有的网友说是签名的问题。

在 downloads 目录下，该文件的总名字是：

	b834d5726f86e08ee41f1901f5856cac72965a4b1a3d60ad767d0bafb513c4c8--OpenJDK8U-jdk_x64_mac_hotspot_8u242b08.pkg.incomplete

我在迅雷下载下来的文件是

	OpenJDK8U-jdk_x64_mac_hotspot_8u242b08.pkg

所以，我将其移动到 /Users/licong/Library/Caches/Homebrew/downloads/ 下，并且，将其命名为：

	b834d5726f86e08ee41f1901f5856cac72965a4b1a3d60ad767d0bafb513c4c8--OpenJDK8U-jdk_x64_mac_hotspot_8u242b08.pkg

将原来的

	b834d5726f86e08ee41f1901f5856cac72965a4b1a3d60ad767d0bafb513c4c8--OpenJDK8U-jdk_x64_mac_hotspot_8u242b08.pkg.incomplete

删掉。但是，我再运行

	brew cask install homebrew/cask-versions/adoptopenjdk8

还是下载文件，于是，我更改了安装方式

	brew cask install adoptopenjdk8

最后成功安装。

<br/>

# brew 的本地安装

<br/>

经过测试，brew install 和 brew cask install 一样。

举一个例子，我在安装 jenkins

	brew install jenkins

但是，安装的速度非常慢，于是，我就查看了 brew 的 下载目录。

而，我的 brew cask 的缓存目录在

	/Users/licong/Library/Caches/Homebrew/downloads/

看到有这样一个文件

	37df13af60d72d1ec9015a3196849941f0ddd8e6e20e8ba247223642f126d6b5--jenkins.war.incomplete

然后我用迅雷，找到 jenkin.war 的资源链接，下载下来

并将文件放在该目录下，命名成

	37df13af60d72d1ec9015a3196849941f0ddd8e6e20e8ba247223642f126d6b5--jenkins.war

同时，我们将

	37df13af60d72d1ec9015a3196849941f0ddd8e6e20e8ba247223642f126d6b5--jenkins.war.imcomplete

给删除掉。

然后我们接着运行

	brew install jenkins

完美解决。
