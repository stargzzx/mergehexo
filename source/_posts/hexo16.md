---
title: hexo | 标签相关的工作
date: 2020-03-07 10:12:06
categories:
- hexo
tags:
- hexo
---
这个难题困扰我很久了，关于标签，我遇到了两个问题。

- 标签云显示不全。明明有这个标签，但是，标签云中不显示，但是，你输入这个标签页，又能出现
- 标签内容错误。有的标签删除了，但是，还会出现在标签云中。

<!-- more -->

<br/>

# 标签云显示不全

<br/>

## 5.x 的解决方案

[参考资料](https://github.com/iissnan/hexo-theme-next/issues/1908)

里面人这么说

>And as we can see, in tagcloud default amount of tags — 40
And in NexT page.swig value is set to 200:

在 hexo-theme-next/layout/page.swig 中

	 {{ tagcloud({min_font: 12, max_font: 30, amount: 200, color: true, start_color: '#ccc', end_color: '#111'}) }} 

我将 amount 变成 500，然后

	hexo clean
	hexo g
	hexo d

那些标签果然都能出现了。

<br/>

# 标签数据紊乱

<br/>

这个问题发生在我的 MacBook 上。

这个主要体现在有的标签删除了，但是依然存在。

其实，网上包括官方都说，这个可能是因为缓存造成的。

我将 db.json 和 .deploy_git 都删除后，然后

	hexo clean
	hexo g

本地查看的时候 hexo s 的时候，发现确实修复了问题，但是 hexo d 部署线上的时候，还是解决不了。

也就是线上和本地表现不一样。

这个问题我至今没有解决。

到后面有时间了，我或许会升级 next 和 hexo 来尝试解决。

## 7.x 的解决方案

这一版的 next 把相关的配置放到了 next 下的 \_config.yml 里面了。

	tagcloud:
	  # All values below are same as default, change them by yourself.
	  min: 12 # Minimun font size in px
	  max: 30 # Maxium font size in px
	  start: "#ccc" # Start color (hex, rgba, hsla or color keywords)
	  end: "#111" # End color (hex, rgba, hsla or color keywords)
	  amount: 600 # Amount of tags, change it if you have more than 200 tags

找到后修改就好了。