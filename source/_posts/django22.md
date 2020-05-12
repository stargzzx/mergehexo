---
title: Django的POST总是出现403错误
date: 2019-09-09 12:31:54
categories:
- python
- 框架
- django
tags:
- Django
- CSRF
- 403
---
这个是由于Django的特性导致的。

<!-- more -->

关于这个东西产生的原因，可以参考我下面的文章：

[什么是CSRF？](https://benpaodewoniu.github.io/2019/09/09/hacker1/)

[Django中的CSRF与AJAX](https://benpaodewoniu.github.io/2019/09/09/django21/)

<br/>

# 解决方案

<br/>

## 全局修改

将 setting.py 下面的那个 csrf 注释掉

![](/images/django/22_0.png)

但是，这样做可能会有风险

## 利用装饰器

### 单独指定csrf验证需要

有时候，我们在全站上关闭了CSRF功能，但是希望某些视图还有CSRF防御，那怎么办呢？

Django为我们提供了一个csrf_protect(view)装饰器，使用起来非常方便，如下所示：

	from django.views.decorators.csrf import csrf_protect
	from django.shortcuts import render
	@csrf_protect
	def my_view(request):
		c = {}
		# ...
		return render(request, "a_template.html", c)
		
现在，虽然全站关掉了csrf，但是my_view视图依然需要进行csrf验证。

### 单独指定忽略csrf验证

有正就有反。在全站开启CSRF机制的时候，有些视图我们并不想开启这个功能。比如，有另外一台机器通过requests库，模拟HTTP通信，以POST请求向我们的Django主机服务器发送过来了一段保密数据。它无法携带CSRF令牌，必然会被403。

这怎么办呢？

在接收这个POST请求的视图上为CSRF开道口子，不进行验证。这就需要使用Django为我们提供的csrf_exempt(view)装饰器了，下面是使用范例：

	from django.views.decorators.csrf import csrf_exempt
	from django.http import HttpResponse
	@csrf_exempt
	def my_view(request):
		return HttpResponse('Hello world')

这下POST数据是没问题了，但是又带来了新的安全问题，需要你自己处理。

### 确保csrf令牌被设置

Django还提供了一个装饰器，确保被装饰的视图在返回页面时同时将csrf令牌一起返回。

这个装饰器是：ensure_csrf_cookie(view)，其使用方法和上面的一样：


	from django.views.decorators.csrf import ensure_csrf_cookie
	from django.http import HttpResponse
	@ensure_csrf_cookie
	def my_view(request):
		return HttpResponse('Hello world')
		
### requires_csrf_token(view)

这个装饰器类似csrf_protect，一样要进行csrf验证，但是它不会拒绝发送过来的请求。

	from django.views.decorators.csrf import requires_csrf_token
	from django.shortcuts import render
	@requires_csrf_token
	def my_view(request):
		c = {}
		# ...
		return render(request, "a_template.html", c)
		
### 在urlconf当中

	from django.views.decorators.csrf import csrf_exempt
	urlpatterns = [
		url(r'^post/get_data/$', csrf_exempt(post_data), name='post_data'),
	]