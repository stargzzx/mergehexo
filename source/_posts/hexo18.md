---
title: hexo | next 5.1 升级至 7.7.2
date: 2020-03-07 14:08:39
categories:
- hexo
tags:
- hexo
---
不知道为什么，本地预览和线上部署不一样，不仅不一样，线上的数据还完全紊乱了。

上网搜的原因是需要 next 升级了。

自这一篇开始， next 从 5.1 更新至 7.7.2

<!-- more -->

<br/>

# 如果你和我出现一样的原因，那么你就需要升级 next

<br/>

打开分类页和标签页，出现 404 ，原因是url 后面自动添加了空格（unicode编码）

上一页和下一页错乱，成为 html 源码

本地预览和线上不一样

<br/>

# 升级

<br/>

[官方升级指南](https://github.com/theme-next/hexo-theme-next/blob/master/docs/zh-CN/UPDATE-FROM-5.1.X.md)

克隆新的仓库到任一异于 next 的目录（如 next-reloaded）【cd 到 themes 中执行】：

	git clone https://github.com/theme-next/hexo-theme-next next-reloaded

执行完这个，我的本地有两个主题，一个是 next(旧的)，一个是 next-reloaded

## 在 Hexo 的主配置文件中设置主题：

在 项目 中的 _config.yml

	
	...
	theme: next-reloaded
	...

## 更新语言设置

从 v6.0.3 版本起，zh-Hans 改名为 zh-CN

升级到 v6.0.3 及以后版本的用户，需要显式修改 Hexo 主配置文件 _config.yml 里的 language 配置，否则语言显示不正确。


## 将原来的配置迁移到新的配置

原来 next 的配置文件在 next 文件夹中 _config.yml，但是，不能把文件进行直接替换，这样会出错。

只能根据自身情况，一个个的替换。

这也是为什么要保留 next 的原因。

我修改的内容如下：

	mathjax 开启
	search 开启
	mermaid 开启

## 迁移样式文件

在老版的 next 中，在 source -> css -> _custom -> custom.styl

这个里面可以自定义样式，但是，新版没有这个文件，即便是新建也读取不到

所以，首先，我们要在 ~\themes\next-reloaded\source\css 的 main.styl 文件末尾加入引用即可。


	@import "_custom/custom.styl";

然后，我们再创建老版的那个目录和文件，把里面的内容复制过来，就完成自定义样式的迁移。

## 修改文字大小

新版默认的字太大了

在themes\next\source\css\_variables下的base.styl中有这么一块：

	// Font size
	$font-size-base           = 1em;
	$font-size-base           = unit(hexo-config('font.global.size'), em) if hexo-config('font.global.size') is a 'unit';
	$font-size-smallest       = .75em;
	$font-size-smaller        = .8125em;
	$font-size-small          = .875em;
	$font-size-medium         = 1em;
	$font-size-large          = 1.125em;
	$font-size-larger         = 1.25em;
	$font-size-largest        = 1.375em;

我将

font-size-base 改成了 0.9 还挺好的

<br/>

# 新版 next 的好处

<br/>

- 渲染速度极快，之前需要 2 分钟，现在只需要 4 秒
- 更加健壮，也对格式要求更加仔细，在老版中一些写的不规范的，在新版中都不能使用，然后我一个文件一个文件（591篇）的修改，耗费了我一整天的时间。（主要是，以前拍版太难看了，借着这次机会，我打算大力的整改一下）
- 哪里出错了，也说明地方（老版就算出错了，也不知道哪里出错，非常不方便）


