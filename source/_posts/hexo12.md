---
title: hexo | 某些小修饰（加粗，颜色变化等）
date: 2020-01-27 19:57:57
categories:
- hexo
tags:
- hexo
---
我们经常在撰写文章的时候需要对文字进行一些修饰。

比如，有的地方进行变颜色，加粗，斜体等，那么在 hexo 中如何做到这些？

<!-- more -->

其实，答案很简单，使用 html 的知识就好了。

# 加粗

	<b>粗体</b>

<b>粗体</b>

# 变颜色

	<p style="color: #FF0000;">红色</p>

<p style="color: #FF0000;">红色</p>

下面是搜集的颜色列表：

{% img /images/hexo/12_0.png %}

# 斜体

	<i>斜体</i>

<i>斜体</i>

# 删除体

	<del>删除体</del>

<del>删除体</del>

# 插入字

	<ins>插入字</ins>

<ins>插入字</ins>

# 上标字
	
	<sup>上标字</sup>123

<sup>上标字</sup>123

# 下标字
	
	123<sub>下标字</sub>

123<sub>下标字</sub>

# 标签

## 数字标签

1. 这是一个标签

2. 这是第二个标签

上面的标签用法是：

	1. 这是一个标签

	2. 这是第二个标签
		数字 + . + 空格

## 符号标签

<li style="margin-left: 40px;font-size: 20px">123</li>

上面的使用这个实现的

	<li style="margin-left: 40px;font-size: 20px">123</li>

