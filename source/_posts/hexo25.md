---
title: hexo | 自定义友链页面
date: 2020-04-23 16:47:59
categories:
- hexo
tags:
- hexo
---
一般 `next` 的友链只是在菜单下方，非常不方便，也不便于组织，所以，我们需要自己定义自己的友链。

<!-- more -->

## 配置 `menu`

主题配置文件_config.yml中menu下添加：

	links: /links/ || link

老版：

`/themes/next/languages/zh-Hans.yml` 文件中 `menu` 下增加中文描述：

新版：

`/themes/next/languages/zh-CN.yml` 文件中 `menu` 下增加中文描述：

	links: 友链

做完这些工作，接下来就是要增加友链页面了.

## 增加友链页面

在你博客的 `source` 这个文件夹中

	mkdir links
	cd links
	touch index.md

然后在 `index.md` 中添加内容就好了。

非常的方便。