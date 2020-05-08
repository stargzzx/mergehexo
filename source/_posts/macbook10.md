---
title: macbook | 更改系统级目录的权限
date: 2020-05-08 13:51:54
categories:
- MacBook
tags:
- MacBook
- brew
---
有的时候，我们需要向 `/usr/bin` 文件夹中转移文件。

但是，在 MacBook 的系统 Mac OSX 10.X 之后，有些目录是不允许读取的，即便是 `root` 权限也不可以。

所以，这一章讲讲述了如何突破这个限制。

<!-- more -->

<br/>

# 参考资料

<br/>

- [Mac下配置Python3+Selenium+Chrome环境](https://blog.csdn.net/u011511921/article/details/82147476)

<br/>

# 关掉rootless

<br/>

要解决这个问题，就必须关掉 `rootless`。

## 重启

重启Mac，按`command+R`进入 rootless 机制

在最开始的时候会让你选择语言设置「就好像是要把电脑清洗一样，不用担心，按照自己的来」

选择完语言后，出现

![](/images/macbook/10_0.jpeg)

在左上角选择终端，输入指令：

	csrutil disable

重启后让机器正常启动，可以在终端上查看rootless状态：

	csrutil status

正常情况下rootless已经关闭

想要重新开启rootless，参照第一步，输入指令：

	csrutil enable

rootless关闭后，即可开始后续操作了。


比如，我想将 chrome 的内核驱动移动到/usr/bin中，需要从终端先进入到chromedriver的目录，然后执行移动命令:

	sudo mv chromedriver /usr/bin

这就完成了。