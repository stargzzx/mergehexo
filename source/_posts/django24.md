---
title: Django中的cookie与session
date: 2019-09-13 16:54:55
categories:
- Django
tags:
- Django
- session
- cookie
---
Django中的cookie与session

<!-- more -->

<br/>

# 原文链接

<br/>

[Django中的cookie与session](https://www.cnblogs.com/chenchao1990/p/5283725.html)

[session会话](http://www.liujiangblog.com/course/django/111)

[Django session](https://code.ziqiangxuetang.com/django/django-session.html)

![](/images/django/24_0.jpg)

HTTP被设计为”无状态”，每次请求都处于相同的空间中。 

在一次请求和下一次请求之间没有任何状态保持，我们无法根据请求的任何方面(IP地址，用户代理等)来识别来自同一人的连续请求。上图很明显的展示了Django的session与cookie的实现原理。服务器会生成两份相同的cookie字符串，一份保存在本地，一份发向请求的浏览器。浏览器将收到的cookie字符串保存下来，当下次再发请求时，会将信息与这段cookie一同发送到服务器，服务器得到这段cookie会与本地保存的那份判断是否相同，如果相同就表示用户已经登录成功，保存用户登录成功的状态。

Django的session保存在数据库中的数据相当于一个大字典，key为cookie的字符串，value仍是一个字典，字典的key和value为用户设置的相关信息。这样就可以方便的存取session里面的信息。

为了实现连接状态的保持功能，网站会通过用户的浏览器在用户机器内被限定的硬盘位置中写入一些数据，也就是所谓的Cookie。通过Cookie可以保存一些诸如用户名、浏览记录、表单记录、登录和注销等各种数据。但是这种方式非常不安全，因为Cookie保存在用户的机器上，如果Cookie被伪造、篡改或删除，就会造成极大的安全威胁，因此，现代网站设计通常将Cookie用来保存一些不重要的内容，实际的用户数据和状态还是以Session会话的方式保存在服务器端。

但是，必须清楚的是Session依赖Cookie！不同的地方在于Session将所有的数据都放在服务器端，用户浏览器的Cookie中只会保存一个非明文的识别信息，比如哈希值。

<br/>

# Cookies

<br/>

cookies 是浏览器为 Web 服务器存储的一小段信息。 每次浏览器从某个服务器请求页面时，它向服务器回送之前收到的cookies。它保存在浏览器下的某个文件夹下。

浏览器下的cookie：

![](/images/django/24_1.jpg)

<br/>

# Session

<br/>

Django的Session机制会向请求的浏览器发送cookie字符串。同时也会保存到本地一份，用来验证浏览器登录是否为同一用户。它存在于服务器，Django默认会把session存入到数据库中。

Session依赖于Cookie，如果浏览器不能保存cooki那么session就失效了。因为它需要浏览器的cooki值去session里做对比。session就是用来在服务器端保存用户的会话状态。

Django提供了一个通用的Session框架，并且可以使用多种session数据的保存方式：

	保存在数据库内
	保存到缓存
	保存到文件内
	保存到cookie内
	
通常情况，没有特别需求的话，请使用保存在数据库内的方式，尽量不要保存到Cookie内。

Django的session框架默认启用，并已经注册在app设置内，如果真的没有启用，那么参考下面的内容添加有说明的那两行，再执行migrate命令创建数据表，就可以使用session了。

	# Application definition
	INSTALLED_APPS = [
		'django.contrib.admin',
		'django.contrib.auth',
		'django.contrib.contenttypes',
		'django.contrib.sessions',    # 这一行
		'django.contrib.messages',
		'django.contrib.staticfiles',
	]
	MIDDLEWARE = [
		'django.middleware.security.SecurityMiddleware',
		'django.contrib.sessions.middleware.SessionMiddleware',  # 这一行
		'django.middleware.common.CommonMiddleware',
		'django.middleware.csrf.CsrfViewMiddleware',
		'django.contrib.auth.middleware.AuthenticationMiddleware',
		'django.contrib.messages.middleware.MessageMiddleware',
		'django.middleware.clickjacking.XFrameOptionsMiddleware',
	]

当session启用后，传递给视图request参数的HttpRequest对象将包含一个session属性，就像一个字典对象一样。你可以在Django的任何地方读写request.session属性，或者多次编辑使用它。

<br/>

# 在视图中使用 session

<br/>

request.session 可以在视图中任何地方使用，它类似于python中的字典

session 默认有效时间为两周，可以在 settings.py 中修改默认值

	# 创建或修改 session：
	request.session[key] = value
	# 获取 session：
	request.session.get(key,default=None)
	# 删除 session
	del request.session[key] # 不存在时报错

<br/>

# session 例子

<br/>

比如写一个不让用户评论两次的应用：

	from django.http import HttpResponse
	def post_comment(request, new_comment):
		if request.session.get('has_commented', False):
			return HttpResponse("You've already commented.")
		c = comments.Comment(comment=new_comment)
		c.save()
		request.session['has_commented'] = True
		return HttpResponse('Thanks for your comment!')

当登陆时验证用户名和密码，并保存用户id在 session 中，这样就可以在视图中用 request.session['member_id']来检查用户是否登陆，当退出的时候，删除掉它。

<br/>

# 结合微信小程序来使用

<br/>

首先，我们先在小程序中获取用户信息。

![](/images/django/24_2.png)

下面这两个按钮，第一个是把用户信息发到Django后台，然后后台注册用户信息，返回给小程序一个 sessionid

第二个按钮是小程序将这个sessionid放在header中，传给django后台，看看服务器的session是否包含了之前的信息。

![](/images/django/24_3.png)

其中，这个页面的 js 如下：

{% codeblock %}
// pages/user/user.js
const app = getApp()
const cookieUtil = require('../../utils/cookie.js')

Page({
  testcookie:function(){
    var header = {}
    var cookie = cookieUtil.getCookieFromStorage()
    header.Cookie = cookie
    var that = this
    var cookie = cookieUtil.getCookieFromStorage()
    console.log(cookie)
    wx.request({
      url: app.globalData.serverUrl + app.globalData.apiVersion + '/user/user',
      method: 'GET',
      header: header,
      success: function (res) {
        console.log(123)
      }
    })
  },
  test:function(){
    wx.login({
      success:function(res){
        var code = res.code
        var appId = app.globalData.appId
        var nickName = app.globalData.userInfo.nickName
        console.log(nickName)
        console.log(app.globalData)

        wx.request({
          url:app.globalData.serverUrl + app.globalData.apiVersion + '/user/test',
          method:'POST',
          data:{
            code: code,
            appId: appId,
            nickName: nickName
          },
          header:{
            'content-type':'application/json'
          },
          success:function(res){
            var cookie = cookieUtil.getSessionIDFromResponse(res)
            cookieUtil.setCookieToStorage(cookie)
            console.log(123)
          }
        })
      }
    })
  }
})
{% endcodeblock %}
其中代码中引入了

	const cookieUtil = require('../../utils/cookie.js')
	
这个cookie.js的作用就是利用本地缓存将服务器发来的session信息保存到 cookie ，其内容如下：

{% codeblock %}
const key = 'cookie'

function getSessionIDFromResponse(res) {
  console.log(res)
  var cookie = res.header['Set-Cookie']
  console.log('get cookie from response: ', cookie)
  return cookie
}

function setCookieToStorage(cookie) {
  try {
    console.log(cookie)
    wx.setStorageSync(key, cookie)
  } catch (e) {
    console.log(e)
  }
}

function getCookieFromStorage() {
  var value = wx.getStorageSync(key)
  console.log(value)
  return value
}
{% endcodeblock %}

然后我们再来说第一个按钮实现的django逻辑，其方法是 test ，就是向后台注册用户信息

内容如下：

{% codeblock %}
def test(request):
    data = request.body.decode('utf-8')
    data = json.loads(data)
    code = data.get('code').strip()
    app_id = data.get('appId').strip()
    nickname = data.get('nickName').strip()

    data = c2s(app_id, code)
    openid = data.get('openid')
    print('get openid: ', openid)

    request.session['open_id'] = openid
    request.session['is_authorized'] = True

    response = wrap_json_response(code=ReturnCode.SUCCESS)
    return JsonResponse(data=response,safe=False)
{% endcodeblock %}

这段代码中，有一个使用 session 的地方就是

	request.session['open_id'] = openid
	request.session['is_authorized'] = True
	
正是因为这两段，所以，后台会向小程序发送sessionid相关信息，如果没有，则不会发送

![](/images/django/24_4.png)

我们还注意到，其中一段话是

	request.session['is_authorized'] = True
	
这是让后台存储这个人已经登入了，不是新用户

下一个按钮的代码如下：

{% codeblock %}
class UserView(View, CommonResponseMixin):
    def get(self, request):
        if not already_authorized(request):
            response = self.wrap_json_response(code=ReturnCode.SUCCESS)
            return JsonResponse(data=response, safe=False)
		print(123)
        open_id = request.session.get('open_id')
        data = {}
        response = self.wrap_json_response(data=data, code=ReturnCode.SUCCESS)
        return JsonResponse(data=response, safe=False)
{% endcodeblock %}

小程序的逻辑如下：

	testcookie:function(){
		var header = {}
		var cookie = cookieUtil.getCookieFromStorage()
		header.Cookie = cookie
		var that = this
		var cookie = cookieUtil.getCookieFromStorage()
		console.log(cookie)
		wx.request({
		url: app.globalData.serverUrl + app.globalData.apiVersion + '/user/user',
		method: 'GET',
		header: header,
		success: function (res) {
			console.log(123)
		}
		})
	},

将前面的 sessionid一起发送过去

	already_authorized(request)

django后台的这句话是在验证有没有 is_authorized ，也就是前面的

	request.session['is_authorized'] = True
	
正好，我们之前是注册过的，所以，在后台会输出

	123
	















