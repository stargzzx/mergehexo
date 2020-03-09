---
title: 博客制作——django admin
date: 2019-08-09 12:37:26
categories:
- Django
tags:
- Django
---
这里讲的是 django admin 模块

<!-- more -->

<br/>

# admin是什么

<br/>

Django的后台管理工具，自动生成

可以读取定义的模型元数据，提供强大的管理使用页面

<br/>

# 为什么使用

<br/>

shell新增文章很复杂

管理页面是基础设施中的重要部分

认证用户、显示管理模型、校验输入等功能类似

<br/>

# 怎么使用

<br/>

创建管理员用户

登录页面管理

输入命令

	python manage.py createsuperuser

![](/images/django/3_0.png)

这里我设置密码是123456

运行

	python manage.py runserver
	
在浏览器输入地址 http://127.0.0.1:8000/admin/

然后填写相关信息，就能进入管理页面

![](/images/django/3_1.png)

<br/>

# 将模型注册到admin里面

<br/>

在 blog 的admin中填写

{% codeblock %}
from django.contrib import admin

<br/>

# Register your models here.

<br/>

from .models import Article

admin.site.register(Article)
{% endcodeblock %}

然后刷新页面就会出现

![](/images/django/3_2.png)

我们就可以在出现的那个 articles 中直接增删改查

<br/>

# 将可视化的体验更好

<br/>

![](/images/django/3_3.png)

我们可以看到文章是以对象信息出现的，我们想让它们显示文章标题，可以修改 blog 下的 models.py

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

    def __str__(self):
        return self.title
{% endcodeblock %}

重新运行命令

	python manage.py runserver
	
然后文章界面就会变成

![](/images/django/3_4.png)

<br/>

# 返回博客数据

<br/>

修改 blog 下的views 文件

{% codeblock %}
from django.shortcuts import render
from django.http import HttpResponse
from blog.models import Article

# Create your views here.

def helloworld(request):
    return HttpResponse("hello world")


def article_content(request):
    article = Article.objects.all()[0]
    title = article.title
    brief_content = article.brief_content
    content = article.content
    article_id = article.article_id
    publish_date = article.publish_date
    return_str = 'title:%s,brief_content:%s,'\
                 'content:%s,article_id:%s,publish_data:%s' % (title,
                                                    brief_content,
                                                    content,
                                                    article_id,
                                                    publish_date)
    return HttpResponse(return_str)
{% endcodeblock %}

配置路由，即修改 blog 下的 urls.py 文件

{% codeblock %}
from django.urls import path,include
import blog.views

urlpatterns = [
    path('hello_world',blog.views.helloworld),
    path('content',blog.views.article_content)
]
{% endcodeblock %}

然后开启服务，在浏览器输入

	http://127.0.0.1:8000/blog/content
	
![](/images/django/3_5.png)




