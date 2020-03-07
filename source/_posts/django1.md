---
title: 博客制作——django模型层
date: 2019-08-09 11:43:24
categories:
- Django
tags:
- Django
---
这里讲了django的模型层。

<!-- more -->

<br/>

# 模型层是什么

<br/>

位于视图和数据库之间

{% img /images/django/1_0.png %}

python对象和数据库表之间转换

<br/>

# 为什么需要模型层

<br/>

可以屏蔽不同数据库之间的差异，可以更加专注于业务逻辑的开发

提供便捷的开发工具

<br/>

# 模型层相关配置

<br/>

项目下的 setting.py 文件

找到 DATABASES 的字符段

{% codeblock %}
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
{% endcodeblock %}

	ENGINE 表明数据库使用的是哪一个驱动
	NAME 数据库的名字，图中所示是 db.sqlite3
	
而当前文件夹下确实有这个文件

{% img /images/django/0_4.png %}

<br/>

# 创建博客文章模型

<br/>
	
	文章标题——文本类型
	文章摘要——文本类型
	文章内容——文本类型
	发布日期——INT类型（自增，主键）
	唯一的ID标记——日期类型

<br/>

# 定义字段

<br/>

	数字类型：IntegerField
	文本类型：TextField
	日期类型：DateTimeField
	自增ID：AutoField
	主键定义：primary_key属性

<br/>

# 模型定义

<br/>

在 blog 的 models.py 文件下定义

{% codeblock %}
from django.db import models

# Create your models here.
class Article(models.Model):
    # 文章ID
    article_id = models.AutoField(primary_key=True)
    # 文章标题
    title = models.TextField()
    # 文章摘要
    brief_content= models.TextField()
    # 文章的主要内容
    content = models.TextField()
    # 文章的发布日期
    publish_date = models.DateTimeField(auto_now=True)
{% endcodeblock %}

<br/>

# 模型迁移

<br/>

将模型的定义保存到数据库里面

需要几个命令

	python manage.py makemigrations

{% img /images/django/1_1.png %}

我们就会发现 blog\migrations\0001_initial.py 创建了。

{% img /images/django/1_2.png %}

然后再执行

	python manage.py migrate

这个命令就是运行迁移文件，把内容同步到数据库里面去

{% img /images/django/1_3.png %}
















