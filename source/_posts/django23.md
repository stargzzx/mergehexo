---
title: django的中间件
date: 2019-09-09 13:02:42
categories:
- Django
tags:
- Diango
- 中间件
---
这个是介绍了Django的中间件。
<!-- more -->
# 中间件
首先，我们知道Django中间件作用于整个项目。
在一个项目中，如果想对全局所有视图函数或视图类起作用时，就可以在中间件中实现，比如想实现用户登录判断，基于用户的权限管理（RBAC）等都可以在Django中间件中来进行操作
Django内置了很多中间件

	MIDDLEWARE_CLASSES = [
		'django.middleware.security.SecurityMiddleware',
		'django.contrib.sessions.middleware.SessionMiddleware',
		'django.middleware.common.CommonMiddleware',
		'django.middleware.csrf.CsrfViewMiddleware',
		'django.contrib.auth.middleware.AuthenticationMiddleware',
		'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
		'django.contrib.messages.middleware.MessageMiddleware',
		'django.middleware.clickjacking.XFrameOptionsMiddleware',
	]
	
# Django中间件的执行流程
Django中间件中最多可以定义5个方法

	process_request
	process_response
	process_view
	process_exception
	process_template_response

Django中间件的执行顺序
## 1
请求进入到Django后，会按中间件的注册顺序执行每个中间件中的process_request方法
如果所有的中间件的process_request方法都没有定义return语句，则进入路由映射，进行url匹配
否则直接执行return语句，返回响应给客户端
## 2
依次按顺序执行中间件中的process_view方法
如果某个中间件的process_view方法没有return语句，则根据第1步中匹配到的URL执行对应的视图函数或视图类
如果某个中间件的process_view方法中定义了return语句，则后面的视图函数或视图类不会执行,程序会直接返回
## 3
视图函数或视图类执行完成之后，会按照中间件的注册顺序逆序执行中间件中的process_response方法
如果中间件中定义了return语句，程序会正常执行，把视图函数或视图类的执行结果返回给客户端
否则程序会抛出异常
## 4
程序在视图函数或视图类的正常执行过程中
如果出现异常，则会执行按顺序执行中间件中的process_exception方法
否则process_exception方法不会执行
如果某个中间件的process_exception方法中定义了return语句，则后面的中间件中的process_exception方法不会继续执行了
## 5
如果视图函数或视图类中使用render方法来向客户端返回数据，则会触发中间件中的process_template_response方法

