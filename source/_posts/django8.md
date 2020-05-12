---
title: 小程序开发——django视图层（Django url配置）
date: 2019-08-20 21:38:11
categories:
- [微信小程序]
- [python,框架,django]
tags:
- Django
- 微信小程序
---
小程序开发——django视图层（Django url配置）

<!-- more -->

<br/>

# Django处理请求的寻址逻辑

<br/>

## 项目层面：根路由模块——urlpatterns

当请求过来的时候，Django首先会寻找根路由模块。

这个模块是配置在配置文件里面的，根据配置，找到路由文件，然后找到 urlpatterns 这个数组

Django会正则匹配每一个数组，直到匹配到第一个符合规则的路由。然后我们就进入了应用层面的路由。

在项目的 setting.py 文件中，有

	ROOT_URLCONF = 'django_test.urls'
	
制定了根路由。

## 应用层面：子路由模块——urlpatterns

子路由的寻址逻辑和根路由的寻址逻辑非常类似。

都是正则匹配数组。

这时候进入了第三个层面：视图层面。

## 试图层面：匹配成功调用指定视图

另外需要注意的是，子路由也可以继续调用子路由，像图中所示：

![](/images/django/8_0.png)

<br/>

# Django的RESTful url设计

<br/>

## API接口的RESTful设计

命名规范

说明接口的类型（视图，模板，API）

	/view
	/template
	/api
	
说明接口的版本（version）

	/view/v1
	/template/v1.2
	/api/v2.0.1
	

## 增删改查的RESTful设计

善于使用HTTP请求中GET、POST、PUT、DELETE方法

	GET：从服务器获取资源
	POST：在服务器新建资源
	PUT：在服务器更新资源
	DELETE：从服务器中删除资源
		比如： GET:/api/v1.0/user
		
url中的过滤信息（分页、指定）

	GET:/api/v1.0/user?page=1
	GET:/api/v1.0/user?name=imooc

## 资源路径的RESTful设计
说明资源类型

	GET:/api/v1.0/resource/image?md5=<md5>
	GET:/spi/v1.0/resource/doc?md5=<md5>

## RESTful url设计

/api/v1.0.2/user?page=1

![](/images/django/8_1.png)

## 实战 url 设计

![](/images/django/8_2.png)

在 project 中的 urls.py 文件中，编写

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    # path('weather/',include('apis.urls'))
    path('api/v1.0',include('django_test.version_1_0'))
]
```

然后，在project中建立 version_1_0.py文件，编写

```python
from django.urls import path,include

urlpattern = [
    path('service/',include('apis.urls'))
]
```

在 apis 中的 urls.py 中编写

```python
from django.urls import path
from .views import weather

urlpatterns = [
    path('weather',weather.weather)
]
然后，在apis 的views中编写页面就好了
```
