---
title: 博客制作——django shell
date: 2019-08-09 12:21:41
categories:
- Django
tags:
- Django
---
这里讲述了 shell 的作用。

<!-- more -->

<br/>

# shell 是什么

<br/>

python的shell适用于交互式的编程

Django 的 shell 也类似，继承了 Django 项目环境

<br/>

# 为什么使用

<br/>

临时性的操作使用 shell 更加方便

小范围的 debug 更简单，不需要运行整个项目来测试

<br/>

# 如何使用

<br/>

通过文章来说明

<br/>

# 进入 shell 环境

<br/>

	python manage.py shell

![](/images/django/2_0.png)

然后引入

	from blog.models import Article
	
如上面所示（上面那个打错了，第一个import 应该是 from）

![](/images/django/2_1.png)

这个是直接存储文章。

![](/images/django/2_2.png)

这个是查询数据库



