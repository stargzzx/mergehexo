---
title: 微信小程序——django的类视图
date: 2019-08-22 14:49:20
categories:
- 微信小程序
tags:
- Diango
- 微信小程序
---
django的类视图
<!-- more -->
# 使用类视图进行HTTP请求方法逻辑分离
## 函数视图的弊端
{% codeblock %}

def image(request):
    if request.method == 'GET':
        md5 = request.GET.get('md5')
        imgfile = os.path.join(settings.IMAGES_DIR, md5 + '.jpg')
        print(imgfile)
        if os.path.exists(imgfile):
            data = open(imgfile, 'rb').read()
            return HttpResponse(content=data, content_type='image/jpg')
            # return FileResponse(open(imgfile, 'rb'), content_type='image/jpg')
        else:
            return Http404()
    elif request.method =='POST':
        pass
    elif request.method == 'PUT':
        pass
    elif request.method == 'DELETE':
        pass
		
{% endcodeblock %}
之前的函数视图都是用 GET 去查询信息，POST 提交信息，PUT 修改信息 ，DELETE 删除信息
但是，如果每一个业务请求都有业务逻辑，那么就会使得这个函数变得非常长。因为，对于相同的函数请求，我们有四种应答。
## 使用类视图
引入

	from django.views import View
	
我们定义一个类：
{% codeblock %}

class ImageView(View):
    def get(self,request):
        md5 = request.GET.get('md5')
        imgfile = os.path.join(settings.IMAGES_DIR, md5 + '.jpg')
        print(imgfile)
        if os.path.exists(imgfile):
            data = open(imgfile, 'rb').read()
            return HttpResponse(content=data, content_type='image/jpg')
            # return FileResponse(open(imgfile, 'rb'), content_type='image/jpg')
        else:
            return Http404()
    def post(self,request):
        message = 'post method success'
        resonse = utils.response.wrap_json_response(message=message)
        return JsonResponse(data=resonse,safe=False)

    def put(self,request):
        message = 'put method success'
        resonse = utils.response.wrap_json_response(message=message)
        return JsonResponse(data=resonse, safe=False)

    def delete(self,request):
        message = 'delete method success'
        resonse = utils.response.wrap_json_response(message=message)
        return JsonResponse(data=resonse, safe=False)
		
{% endcodeblock %}
这个类里面有get,post,put,delete方法，当对应的请求来的时候，会自动调用相应的方法，然后，我们修改应用的路由
类的路由，和方法的路由不一样，需要调用一个这个类，然后调用这个类的自有方法
{% codeblock %}

urlpatterns = [
    # path('', weather.helloworld)
    path('weather', weather.weather),
    path('menu', menu.get_menu),
    path('image',image.ImageView.as_view())
]

{% endcodeblock %}
{% img /images/django/12_0.png %}
# python Mixin模式与类视图
## python的Mixin模式
{% img /images/django/12_1.png %}
传统的继承好像左边的图，是 is a 的关系。
喜鹊是一个动物。
但是，如果继承链条非常长的话，就会导致这个业务逻辑越来越负责，所以，python引入了Mixin模式，他是 it can 的关系。
这样就会简化代码。
之前的代码结构，image.py调用response.py的函数。
### image.py
{% img /images/django/12_2.png %}
### response.py
{% img /images/django/12_3.png %}
注意，这个类有一个修饰

	@classmethod
	

现在修改代码，采用 Mixin 的方式。
### response.py
{% img /images/django/12_4.png %}
### image.py
{% img /images/django/12_5.png %}













