---
title: hexo | 文章加密
date: 2020-04-26 17:50:43
categories:
- hexo
tags:
- hexo
---
有的时候，我们有些文章不希望公开，所以需要进行加密，经过我的一番探究，找到了一个非常好用的 hexo 加密插件。

<!-- more -->

<br/>

# 参考资料

<br/>

[hexo-blog-encrypt](https://github.com/MikeCoder/hexo-blog-encrypt/blob/master/ReadMe.zh.md)

<br/>

# 使用

<br/>

进入 blog 的根目录，使用

	npm install --save hexo-blog-encrypt

修改 `_config.yml`

添加

	encrypt:
		enable: true

在你的文章的头部添加上对应的字段，如 

- `password`
- `abstract`
- `message`

<br/>

	---
	title: hello world
	date: 2016-03-30 21:18:02
	password: test
	abstract: Welcome to my blog, enter password to read.
	message: Welcome to my blog, enter password to read.
	---

在官方文档中有更详细的说明，另外，想要生效，需要

	hexo clean
	hexo g
	hexo s
		本地查看

感谢插件作者。

<br/>

# 本博客加密文章等级公示

<br/>

|加密等级|加密权限|加密内容类型|
|---|---|---|
|0|绝对不对外公开|***|
|1|不对陌生人公开，熟人阅读后，会修改密码，不涉及商业|***|
|2|不对非利益方公开|涉及商业|
|3|仅对私人或者好友开放|原作者或者其他可能类似侵权的原因|
|4|可选择性公开|个人总结、个人不方便公开的生活经历等|
|5|未来会自动公开|主要是对于一些原创文章的保护|

<br/>

# 与 mathjax 的冲突

<br/>

	password: ***
	abstract: *****
	message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
	mathjax: true

输完密码进去后，发现，表达式都是源代码形式，但是，不用怕，只需要，再次刷新就好了。
