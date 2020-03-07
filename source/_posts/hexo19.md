---
title: hexo | 增加插件和修改配置怎么保持本地和线上一样
date: 2020-03-08 00:16:15
categories:
- hexo
tags:
- hexo
---
针对 next 7.7.2 版本（此方法不适用 5.x 版本，因为，这个版本已经被官方弃用）

<!-- more -->

每次增加插件和修改项目或者主题配置，我们应该先删除缓存

	rm -rf .deploy_git
	rm -rf db.json

然后，我们再执行

	hexo clean
	hexo g

当我们执行

	hexo d

的时候，就是将我们本地所有的文件全部替换线上文件。
