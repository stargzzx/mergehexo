---
title: 博客制作——django入门
date: 2019-08-07 13:28:15
categories:
- Django
tags:
- Django
---
这是入门教程。

<!-- more -->

<br/>

# 安装

<br/>

直接输入

	pip install django
	
我们检验是否安装成功可以使用

	diango-admin
		（注意中间没有空格）
		
成功安装会出现下面的图片。

![](/images/django/0_0.png)

图中出现了很多命令

	startproject # 创建一个Django项目
	startapp     # 创建一个Django应用
	check        # 校验项目完整性
	runserver    # 本地简易运行Django项目
	shell        # 进入Django项目的 python shell 环境
	test         # 执行Django用例测试
	数据库相关命令
	makemigrations# 创建模型变更的迁移文件
	migrate       # 执行上一个命令创建的迁移文件
	dumpdata      # 把数据库数据导出到文件
	loaddata      # 把文件数据导入到数据库

<br/>

# 创建一个项目

<br/>

	django-admin startproject 项目名
		项目名不能是 django
这有一点需要特别注意，就是执行上面的这个命令之后，创建的项目文件夹就在该路径下

比如

![](/images/django/0_1.png)

所以，我们要看好路径。

<br/>

# 运行项目

<br/>

	python manage.py runserver
	
![](/images/django/0_2.png)

<br/>

# 项目目录

<br/>

	配置文件：settings.py
	路由配置文件：urls.py
	管理文件：manage.py

<br/>

# Django应用和Django项目

<br/>

一个Django项目就是一个基于Django的Web应用

一个Django应用就是一个可重用的python软件包

每个应用可以自己管理模型、视图、模板、路由和静态文件等。

一个项目包含一组配置和若干个应用

![](/images/django/0_3.png)

从上图中可以看出，应用可以在不同的项目下重叠。

<br/>

# Django 应用创建

<br/>

	python manage.py startapp 应用名字
	
会在当前目录下创建一个 blog 应用

![](/images/django/0_4.png)

# Django 应用的目录

即blog下的文件作用

	views.py： 视图处理地方
	models.py： 定义应用模型
	admin.py： admin模块管理对象
	apps.py：声明应用的地方
	tests.py： 编写应用测试用例的地方
	urls.py：（自行创建）管理路由的地方
	
<br/>

# Django视图

<br/>

## 修改应用中的views文件

也就是 blog下面的views.py 文件。

代码如下：

{% codeblock %}
from django.shortcuts import render
from django.http import HttpResponse

# Create your views here.

def helloworld(request):
    return HttpResponse("hello world")
{% endcodeblock %}

## 配置路由

路由有两个，一个是项目，一个是APP

我们先配置APP的路由

### 配置APP路由

在 blog 下建立一个 urls.py 文件

写下这写code

下面的意思就是如果路由里含有hello_world 就跳转到 blog.views.helloworld 页面

{% codeblock %}
from django.urls import path,include
import blog.views

urlpatterns = [
    path('hello_world',blog.views.helloworld)
]
{% endcodeblock %}

## 配置项目路由

下面的代码意思就是如果url 含有 blog 的话，就跳转到 blog 应用下的 urls 路由规则

{% codeblock %}
from django.contrib import admin
from django.urls import path,include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/',include('blog.urls'))
]
{% endcodeblock %}

## 配置setting文件

在 INSTALLED_APPS 添加下面的东西

{% codeblock %}
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # myapp
    'blog.apps.BlogConfig'
]
{% endcodeblock %}

这个就是配置 blog APP，至于为什么是 BlogConfig 是因为在

blog文件夹下的 apps.py 文件的内容

{% codeblock %}
from django.apps import AppConfig


class BlogConfig(AppConfig):
    name = 'blog'
{% endcodeblock %}

## 运行

	python manage.py runserver
	
然后进入路由

	http://127.0.0.1:8000/blog/hello_world

就可以看见 hello world 了

![](/images/django/0_5.png)











