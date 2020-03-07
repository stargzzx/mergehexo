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

<br/>

# 加粗

<br/>

	<b>粗体</b>

<b>粗体</b>

<br/>

# 变颜色

<br/>

	<p style="color: #FF0000;">红色</p>

<p style="color: #FF0000;">红色</p>

下面是搜集的颜色列表：

{% img /images/hexo/12_0.png %}

<br/>

# 斜体

<br/>

	<i>斜体</i>

<i>斜体</i>

<br/>

# 删除体

<br/>

	<del>删除体</del>

<del>删除体</del>

<br/>

# 插入字

<br/>

	<ins>插入字</ins>

<ins>插入字</ins>

<br/>

# 上标字

<br/>
	
	<sup>上标字</sup>123

<sup>上标字</sup>123

<br/>

# 下标字

<br/>
	
	123<sub>下标字</sub>

123<sub>下标字</sub>

<br/>

# 标签

<br/>

## 数字标签

1. 这是一个标签

2. 这是第二个标签

上面的标签用法是：

	1. 这是一个标签

	2. 这是第二个标签
		数字 + . + 空格

## 符号标签

<li style="margin-left: 40px;font-size: 15px">123</li>

上面的使用这个实现的

	<li style="margin-left: 40px;font-size: 15px">123</li>

我们的 li 标签要放在一行中，不然，每一个 li 标签，会被添加 <br/>

<li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li>

	<li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li>

如果不放在一行会出现

<li style="margin-left: 40px;font-size: 15px">123</li>
<li style="margin-left: 40px;font-size: 15px">123</li>
<li style="margin-left: 40px;font-size: 15px">123</li>

很难看

<br/>

# 好看的排列

<br/>

当我们只用 # 这个标签的时候，最好添加 <br/>

	<br/>
	
	# ***
	
	<br/>

这样整体排列很好看。

## 图形标签

一个非常偶然的机会发现官方的图形标签。

- 1
    - 1
        - 1
            - 1
- 2
- 3

其上面的表示方式如下：

	- 1
	    - 1
	        - 1
	            - 1
	- 2
	- 3


