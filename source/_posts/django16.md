---
title: Django报错TypeError:xxx got multiple valuesy for argument 'xxx'
date: 2019-08-23 14:58:30
categories:
- Django
tags:
- Django
- Django错误
---
Django 报错 TypeError: xxx() got multiple values for argument 'xxx'
<!-- more -->
这个错误常见于定义视图函数，也就是 views 中的相应的请求的处理函数

一般情况下，我们会直接在视图函数中定义路径参数，比如下面这个

	from django.shortcuts import render
	from django.http import HttpResponse
	# Create your views here.
	def gen_qrcode(text='xx'):
		return HttpResponse(str('qrcode'))
		
但实际上， Django 的要求是第一个参数必须是 HTTP 请求对象 request
因此，正确的做法是

	from django.shortcuts import render
	from django.http import HttpResponse
	# Create your views here.
	def gen_qrcode(request, text='xx'):
		return HttpResponse(str('qrcode'))
