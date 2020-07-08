---
title: idea | lombok 不生效
date: 2020-07-08 14:51:24
categories:
- [java,IDE,idea]
tags:
- idea
---
在使用的过程中，明明已经安装了 lombok 为什么 idea 中，代码显示找不到该方法。

原因是没有安装 lombok 的插件。

<!-- more -->

先说一下环境，我是在 MacBook 中。

<br/>

# 参考资料

<br/>

- [IntelJ idea下lombok 不生效的问题](https://blog.csdn.net/lwang_IT/article/details/83016597)

<br/>

# 安装

<br/>

整个过程没有上面教程中那么繁琐。

	Preferences -> Plugins

可以看到右边有两个选项

- Marketplace
- Installed

我们查看 Installed 搜索一下，确实没有 `lombok`。

然后，我们在 `Marketplace` 搜索安装，然后重启 `Idea` 即可。