---
title: 微信小程序开发——会话服务
date: 2019-09-07 10:23:34
categories:
- 微信小程序
tags:
- Django
- 微信小程序
---
微信小程序开发——会话服务

<!-- more -->

![](/images/django/19_0.png)

<br/>

# 什么是状态——有状态服务VS无状态服务

<br/>

上下文关系。

两个向服务器发送的请求如果具备上下文关系的话，那就是有状态服务。

这两个请求之间可能会有上下文关系的信息，我们称之为凭证。

<br/>

# HTTP协议中的状态

<br/>

HTTP中有两个对象，一个是客户端，一个是服务端

![](/images/django/19_1.png)

## 服务器

看图中的红线框，是服务器要你下次返回的时候，需要携带的信息。

![](/images/django/19_2.png)

## 客户端

客户端返回的信息。

![](/images/django/19_3.png)

<br/>

# 小程序中的状态

<br/>

但是小程序的状态并不能直接套用HTTP中的状态。

因为小程序还有一个中间人就是微信服务器。

以Django后台为例，小程序是先把信息传给微信服务器，然后微信服务器再将信息传给Django后台。

![](/images/django/19_4.png)

但是，微信的服务器只负责转发，而不能对 cookies 做任何处理，包括存储。

所以，我们就让小程序用 storage 保存 cookies 然后用小程序发送就好了。

![](/images/django/19_5.png)

## 小程序 Storage 存储 Cookies

![](/images/django/19_2.png)

从应答报文的头部，把 Cookies 取出来。

## Django的Session中间件

![](/images/django/19_11.png)

我们要把后台的 setting.py 的 Session 给弄好。

![](/images/django/19_6.png)

## 整个过程

### Django的后台配置

然后，我们在 views.py 的内容如下：

```python
from django.shortcuts import render
# Create your views here.
from django.http import JsonResponse
from utils.response import wrap_json_response, ReturnCode


def test_session(request):
    request.session['message'] = 'Test Django Session OK!'
    response = wrap_json_response(code=ReturnCode.SUCCESS)
    return JsonResponse(data=response, safe=False)


def test_session2(request):
    print('session content: ', request.session.items())
    response = wrap_json_response(code=ReturnCode.SUCCESS)
    return JsonResponse(data=response, safe=False)
```

其中，我们可以看到

test_session 是为了设置 session

### 小程序页面

![](/images/django/19_7.png)

![](/images/django/19_8.png)

#### cookie.js

我们定义了一个使用 storage 存储 cookie 的文件

```python
const key = 'cookie'

function getSessionIDFromResponse(res){
  var cookie = res.header['Set-Cookie']
  console.log('get cookie from response: ' + cookie)
  return cookie
}

function setCookieToStorage(cookie) {
  try{
    wx.setStorageSync(key, cookie)
  }catch(e){
    console.log(e)
  }
}

function getCookieFromStorage() {
  var value = wx.getStorageSync(key)
  return value
}

module.exports = {
  setCookieToStorage: setCookieToStorage,
  getCookieFromStorage: getCookieFromStorage,
  getSessionIDFromResponse: getSessionIDFromResponse
}
```

我们使用 

	module.exports
	

将这个 js 定义的方法，进行外抛，可以在别的进行使用。

其中这个页面这小程序里是 homepage。

#### hompage.wxml

```python
<!--pages/homepage/homepage.wxml-->
<view class="container">
  <view class="page__bd page__bd_spacing button-sp-area">
    <button class="weui-btn mini-btn" type="primary" bindtap='onReadCookies'>获取Cookies演示</button>
  </view>
</view>
```

#### homepage.js

这里有一个语法可以了解一下：

	const cookieUtil = require('../../utils/cookie.js')
	
然后我们就能调用那个页面的方法了

```python
// pages/homepage/homepage.js

const app = getApp()
const cookieUtil = require('../../utils/cookie.js')

Page({

  onReadCookies: function (){
    wx.request({
      url: app.globalData.serverUrl + app.globalData.apiVersion + '/auth/test',
      success: function(res){
        var cookie = cookieUtil.getSessionIDFromResponse(res)
        console.log(cookie)
        cookieUtil.setCookieToStorage(cookie)

        var newCookie = cookieUtil.getCookieFromStorage()

        var header = {}
        header.Cookie = newCookie

        wx.request({
          url: app.globalData.serverUrl + app.globalData.apiVersion + '/auth/test2',
          header: header,
          success: function(res){

          }
        })
      }
    })
  }
})
```

整个的页面逻辑如下：

我们点击那个绿色的按钮，会向

	127.0.0.1:8000/v1.0/auth/test
	
发送一个请求，这个请求将会返回Django包含的信息，即 ‘Test Django Session OK’

然后，我们将这个信息装在 storage 中，然后再次请求传给

	127.0.0.1:8000/v1.0/auth/test2

我们就能看到在界面中打出上一个Django返回的信息。

![](/images/django/19_9.png)

![](/images/django/19_10.png)
