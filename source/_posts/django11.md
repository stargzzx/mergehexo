---
title: 微信小程序——实现图文开发
date: 2019-08-21 16:26:42
categories:
- 微信小程序
tags:
- Django
- 微信小程序
---
微信小程序——实现图文开发

<!-- more -->

<br/>

# 图文消息的开发逻辑

<br/>

文本消息和图文消息对比

![](/images/django/11_0.png)

<br/>

# Django的图片消息开发

<br/>

![](/images/django/11_1.png)

我们建立了一个文件夹，叫做 resources ，然后里面有一个 images 文件夹。

里面存放图片，其名字是图片的 md5 。

首先设置路由，即存放 images 的路径。

在 project 的 settings.py 中，加上

	RESOURCES_DIR = os.path.join(BASE_DIR, 'resources')
	IMAGES_DIR = os.path.join(RESOURCES_DIR, 'images')

然后，我们在 apis 应用中的 views 文件夹内，创建了 image.py

{% codeblock %}
import os
from django.http import Http404, HttpResponse, FileResponse
from backend import settings


def image(request):
    if request.method == 'GET':
        md5 = request.GET.get('md5')
        imgfile = os.path.join(settings.IMAGES_DIR, md5 + '.jpg')
		# 这个 settings 是我们在之前定义的
        print(imgfile)
        if os.path.exists(imgfile):
            data = open(imgfile, 'rb').read()
            # return HttpResponse(data, content_type='image/jpg')
            return FileResponse(open(imgfile, 'rb'), content_type='image/jpg')
			# 为了说明内容是图片，所以增加了 content_type
			# 上面两个都可以
        else:
            return Http404()
    elif request.method == 'POST':
        pass
{% endcodeblock %}

我们继续添加 apis 应用下的 urls.py 的路由

{% codeblock %}
from django.urls import path

from .views import weather, menu, image

urlpatterns = [
    # path('', weather.helloworld)
    path('weather', weather.weather),
    path('menu', menu.get_menu),
    path('image', image.image)
]
{% endcodeblock %}

然后我们通过浏览器访问

	http://127.0.0.1:8000/api/v1.0/service/image?md5=1ad78e3e075fd648882ba5299728369b

我们就能看到图片了。

<br/>

# 拿到图片信息

<br/>

我们在 apis 的 image.py 再次添加一个方法

{% codeblock %}
def image_text(request):
    if request.method == 'GET':
        md5 = request.GET.get('md5')
        imgfile = os.path.join(settings.IMAGES_DIR, md5 + '.jpg')
        print(imgfile)
        if not os.path.exists(imgfile):
            return utils.response.wrap_json_response(
                code=utils.response.ReturnCode.RESOURCES_NOT_EXISTS
            )
        else:
            response_data = {}
            response_data['name'] = md5 + '.jpg'
            response_data['url'] = '/service/image?md5=%s' % (md5)
            response = utils.response.wrap_json_response(data = response_data)
            return JsonResponse(data=response,safe=False)		
{% endcodeblock %}

这样我们访问

	http://127.0.0.1:8000/api/v1.0/service/imagetext?md5=1ad78e3e075fd648882ba5299728369b
	
的时候，得到

![](/images/django/11_2.png)

当客户端拿到这个数据的时候，就会找到图片的url，然后请求就好了。

![](/images/django/11_3.png)





