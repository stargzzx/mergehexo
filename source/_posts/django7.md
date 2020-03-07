---
title: 小程序开发——django视图层（Request Response对象）
date: 2019-08-20 11:25:47
categories:
- 微信小程序
tags:
- Django
- 微信小程序
---
这是django的视图层。

<!-- more -->

<br/>

# Request Response对象

<br/>

## HTTP请求与Django Request对象

### HTTP请求

	请求方法
	headers
	请求参数
	cookies
	请求端信息
	
上面这5个对应于Django Request的数据是：

	请求方法——Request对象的method属性
	客户端信息——Request对象的META属性
	cookies——Request对象的COOKIES属性
	请求参数——Request对象中QueryDict
	
我们创建了一个叫做 django_test 的项目，关于如何创建项目，请参考我写的相关文章。

然后，我创建了一个叫做 apis 的 app 。

里面的目录结构如下

{% img /images/django/7_0.png %}

值得一提的是，原来要创建一个 app 的话，会在 apis 下面创建一个 views.py 的文件，但是，我们创建了一个叫做 views 的文件夹，用来存放视图文件。

在配置路由的时候会出现

	from .views import weather
	
所以，我们要把 views.py 删掉，不然会使得路由冲突。

在 apis 中的 urls.py 中，我们编写

{% codeblock %}	
from django.urls import path
from .views import weather

urlpatterns = [
	path('',weather.helloworld)
]
{% endcodeblock %}

在总的 urls.py 下我们编写

{% codeblock %}	
from django.contrib import admin
from django.urls import path,include

urlpatterns = [
	path('admin/', admin.site.urls),
	path('weather/',include('apis.urls'))
]
{% endcodeblock %}

然后在 setting.py 中，我们注释 MIDDLEWARE 中的

	'django.middleware.csrf.CsrfViewMiddleware',
	

我们在 views 文件夹之下，创建 weather.py 文件，里面编写

{% codeblock %}
from django.http import HttpResponse

def helloworld(request):
    print('request method:',request.method)
    print('request META:',request.META)
    print('request cookies:',request.COOKIES)
    print('request QueryDict:',request.GET) # 这个是打印 GET 传过去的参数
    return HttpResponse('OK')	
{% endcodeblock %}

然后我们运行

	python manage.py runserver
	
通过 postman 软件，我们访问，就能得到正确得回应

{% img /images/django/7_1.png %}

我们也可以在控制台看见输入的信息，在这里就不展示了。

## HTTP应答与Django Response对象

### 状态码

Response对象中的status属性

### 应答内容

Response对象中的content属性

### 内容格式

眼伸的Response字类——（JosonResponse、FileResponse）

### 应用

	from django.http import HttpResponse,JsonResponse,FileResponse
	
{% codeblock %}
from django.http import HttpResponse,JsonResponse,FileResponse

def helloworld(request):
    print('request method:',request.method)
    print('request META:',request.META)
    print('request cookies:',request.COOKIES)
    print('request QueryDict:',request.GET) # 这个是打印 GET 传过去的参数
    #return HttpResponse(content='hello Django Response',status='200')
    m = {
        'message': "hello Django Response"
    }
    return JsonResponse(data=m,safe=False,status=200)
    # 这个 safe 属性为 true 的时候，data 只能转换字典对象，如果为 False 则不检查
{% endcodeblock %}

## 实战：实现天气查询应用

{% img /images/django/7_2.png %}

使用了第三方聚合数据的API

在总目录下新建一个 thirdparty 文件夹，里面有两个文件 __init__.py 里面什么内容都没有，juhe.py 里面封装了API代码。

目录结构如下：

{% img /images/django/7_3.png %}

juhe.py的内容如下：

{% codeblock %}
import json
import requests


def weather(cityname):
    """
    :param cityname: 城市名字
    :return: 返回实况天气
    """
    key = '9a3e1fa6cb79d69f1594af5cb219a469'
    api = 'http://v.juhe.cn/weather/index'
    params = 'cityname=%s&key=%s' % (cityname, key)
    url = api + '?' + params
    print(url)
    response = requests.get(url=url)
    json_data = json.loads(response.text)
    print(json_data)
    result = json_data.get('result')
    sk = result.get('sk')
    response = dict()
    response['temperature'] = sk.get('temp')
    response['wind_direction'] = sk.get('wind_direction')
    response['wind_strength'] = sk.get('wind_strength')
    response['humidity'] = sk.get('humidity')  # 湿度
    response['time'] = sk.get('time')
    return response


if __name__ == '__main__':
    data = weather('深圳')
{% endcodeblock %}

我们将 apis 下views 文件夹内的 weather.py 文件内容改成

{% codeblock %}
from django.http import HttpResponse,JsonResponse,FileResponse
from thirdparty import juhe

def helloworld(request):
    print('request method:',request.method)
    print('request META:',request.META)
    print('request cookies:',request.COOKIES)
    print('request QueryDict:',request.GET) # 这个是打印 GET 传过去的参数
    #return HttpResponse(content='hello Django Response',status='200')
    m = {
        'message': "hello Django Response"
    }
    return JsonResponse(data=m,safe=False,status=200)
    # 这个 safe 属性为 true 的时候，data 只能转换字典对象，如果为 False 则不检查

def weather(request):
    if request.method == 'GET':
        city = request.GET.get('city')
        data = juhe.weather(city)
        return JsonResponse(data=data,status=200)
    else:
        print("no support method")
{% endcodeblock %}

然后 apis 下的 urls.py 文件改成

{% codeblock %}
from django.urls import path
from .views import weather

urlpatterns = [
    path('',weather.weather)
]
{% endcodeblock %}

最后我们通过 postman 发送 127.0.0.1:8000/weather/?icity="深圳"

但是，结果是失败的，可能的原因是，这个juhe.py 的代码已经过期。

我们继续完善，加上 POST 方法

{% codeblock %}
from django.http import HttpResponse,JsonResponse,FileResponse
from thirdparty import juhe
import json
def helloworld(request):
    print('request method:',request.method)
    print('request META:',request.META)
    print('request cookies:',request.COOKIES)
    print('request QueryDict:',request.GET) # 这个是打印 GET 传过去的参数
    #return HttpResponse(content='hello Django Response',status='200')
    m = {
        'message': "hello Django Response"
    }
    return JsonResponse(data=m,safe=False,status=200)
    # 这个 safe 属性为 true 的时候，data 只能转换字典对象，如果为 False 则不检查

def weather(request):
    if request.method == 'GET':
        city = request.GET.get('city')
        data = juhe.weather(city)
        return JsonResponse(data=data,status=200)
    elif request.method == 'POST':
        received_body = request.body
        receviced_body = json.loads(received_body)
        cities = received_body.get('cities')
        response_data = []
        for city in cities:
            result = juhe.weather(city)
            response_data.append(result)
        return JsonResponse(data=response_data,status=200,safe=False)
{% endcodeblock %}

利用 postman 发送 POST 请求

{% img /images/django/7_4.png %}