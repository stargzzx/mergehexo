---
title: Django中的CSRF与AJAX
date: 2019-09-09 09:29:26
categories:
- Django
tags:
- Django
- CSRF
- 中间件
---
CSRF是黑客攻击的常见手段，而Django却有应对的机制。

<!-- more -->

<br/>

# 原文链接

<br/>

[CSRF与AJAX](http://www.liujiangblog.com/course/django/179)

[详解Django的CSRF认证](https://www.cnblogs.com/renpingsheng/p/9756051.html)

如果你要想了解什么是CSRF的话，可以看看我下面的文章：

[什么是CSRF](https://benpaodewoniu.github.io/2019/09/09/hacker1/)

<br/>

# csrf原理

<br/>

csrf要求发送post,put或delete请求的时候，是先以get方式发送请求，服务端响应时会分配一个随机字符串给客户端，客户端第二次发送post,put或delete请求时携带上次分配的随机字符串到服务端进行校验

<br/>

# Django中的CSRF中间件

<br/>

首先，我们知道Django中间件作用于整个项目。

在一个项目中，如果想对全局所有视图函数或视图类起作用时，就可以在中间件中实现，比如想实现用户登录判断，基于用户的权限管理（RBAC）等都可以在Django中间件中来进行操作

Django内置了很多中间件,其中之一就是CSRF中间件

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
	
上面第四个就是Django内置的CSRF中间件

<br/>

# 基本使用

<br/>

默认情况下，使用django-admin startproject xxx命令创建工程时，CSRF防御机制就已经开启了。

如果没有开启，请在MIDDLEWARE设置中添加'django.middleware.csrf.CsrfViewMiddleware'。

上面的那个选项是在项目中的 setting.py 文件内

![](/images/django/21_0.png)

>对于GET请求，一般来说没有这个问题，CSRF通常是针对POST方法的！

在含有POST表单的模板中，需要在其<form>表单元素内部添加csrf_token标签。

如下所示：

![](/images/django/21_1.png)

	<form id="comment_form" action="#"></form>
	{% csrf_token %}   就是这个
	<p>姓名: <input type="text" name="useranme" id="username"></p>
	<p>内容: <textarea name="content" id="content" rows="5" cols="30"></textarea></p>
	<p><input type="button", id="send" value="提交"></p> 
	
这样，当表单数据通过POST方法，发送到后台服务器的时候，除了正常的表单数据外，还会携带一个CSRF令牌随机字符串，用于进行csrf验证。其实没有多么麻烦和复杂，对么？如果表单中没有携带这个csrf令牌，你将会获得一枚403奖章。

额外提示：对于初学者，要明白一件事情，就是我们上面讲的都是Django项目自己内部的事务，不涉及与外界的关系。例如，你不能把上面那个表单发往百度，百度会懵逼的，你这发的啥？其次，那样也不安全，可能引起CSRF信息泄露而导致自己的站点出现漏洞。

<br/>

# AJAX

<br/>

我们知道，在前端的世界，有一种叫做AJAX的东西，也就是“Asynchronous Javascript And XML”（异步 JavaScript 和 XML），经常被用来在不刷新页面的情况下，提交和请求数据。如果我们的Django服务器接收的是一个通过AJAX发送过来的POST请求的话，那么将很麻烦。

为什么？因为AJAX中，没有办法像form表单中那样携带

	{% csrf_token %}
	
令牌。

那怎么办呢？

好办！在你的前端模版的JavaScript代码处，添加下面的代码：

```python
// using jQuery
function getCookie(name) {
    var cookieValue = null;
    if (document.cookie && document.cookie !== '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = jQuery.trim(cookies[i]);
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) === (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}
var csrftoken = getCookie('csrftoken');

function csrfSafeMethod(method) {
    // 这些HTTP方法不要求CSRF包含
    return (/^(GET|HEAD|OPTIONS|TRACE)$/.test(method));
}
$.ajaxSetup({
    beforeSend: function(xhr, settings) {
        if (!csrfSafeMethod(settings.type) && !this.crossDomain) {
            xhr.setRequestHeader("X-CSRFToken", csrftoken);
        }
    }
});
```

上面代码的作用就是让你的ajax的POST方法带上CSRF需要的令牌，它依赖Jquery库，必须提前加载Jquery。这是Django官方提供的解决方案哦，^-^。

<br/>

# Django中间件的执行过程

<br/>

[django的中间件](https://benpaodewoniu.github.io/2019/09/09/django23/)

<br/>

# Django CSRF中间件的源码解析

<br/>

Django CSRF中间件的源码

```python
class CsrfViewMiddleware(MiddlewareMixin):

    def _accept(self, request):
        request.csrf_processing_done = True
        return None

    def _reject(self, request, reason):
        logger.warning(
            'Forbidden (%s): %s', reason, request.path,
            extra={
                'status_code': 403,
                'request': request,
            }
        )
        return _get_failure_view()(request, reason=reason)

    def _get_token(self, request):
        if settings.CSRF_USE_SESSIONS:
            try:
                return request.session.get(CSRF_SESSION_KEY)
            except AttributeError:
                raise ImproperlyConfigured(
                    'CSRF_USE_SESSIONS is enabled, but request.session is not '
                    'set. SessionMiddleware must appear before CsrfViewMiddleware '
                    'in MIDDLEWARE%s.' % ('_CLASSES' if settings.MIDDLEWARE is None else '')
                )
        else:
            try:
                cookie_token = request.COOKIES[settings.CSRF_COOKIE_NAME]
            except KeyError:
                return None

            csrf_token = _sanitize_token(cookie_token)
            if csrf_token != cookie_token:
                # Cookie token needed to be replaced;
                # the cookie needs to be reset.
                request.csrf_cookie_needs_reset = True
            return csrf_token

    def _set_token(self, request, response):
        if settings.CSRF_USE_SESSIONS:
            request.session[CSRF_SESSION_KEY] = request.META['CSRF_COOKIE']
        else:
            response.set_cookie(
                settings.CSRF_COOKIE_NAME,
                request.META['CSRF_COOKIE'],
                max_age=settings.CSRF_COOKIE_AGE,
                domain=settings.CSRF_COOKIE_DOMAIN,
                path=settings.CSRF_COOKIE_PATH,
                secure=settings.CSRF_COOKIE_SECURE,
                httponly=settings.CSRF_COOKIE_HTTPONLY,
            )
            patch_vary_headers(response, ('Cookie',))

    def process_request(self, request):
        csrf_token = self._get_token(request)
        if csrf_token is not None:
            # Use same token next time.
            request.META['CSRF_COOKIE'] = csrf_token

    def process_view(self, request, callback, callback_args, callback_kwargs):
        if getattr(request, 'csrf_processing_done', False):
            return None

        if getattr(callback, 'csrf_exempt', False):
            return None

        if request.method not in ('GET', 'HEAD', 'OPTIONS', 'TRACE'):
            if getattr(request, '_dont_enforce_csrf_checks', False):
                return self._accept(request)

            if request.is_secure():
                referer = force_text(
                    request.META.get('HTTP_REFERER'),
                    strings_only=True,
                    errors='replace'
                )
                if referer is None:
                    return self._reject(request, REASON_NO_REFERER)

                referer = urlparse(referer)

                if '' in (referer.scheme, referer.netloc):
                    return self._reject(request, REASON_MALFORMED_REFERER)

                if referer.scheme != 'https':
                    return self._reject(request, REASON_INSECURE_REFERER)

                good_referer = (
                    settings.SESSION_COOKIE_DOMAIN
                    if settings.CSRF_USE_SESSIONS
                    else settings.CSRF_COOKIE_DOMAIN
                )
                if good_referer is not None:
                    server_port = request.get_port()
                    if server_port not in ('443', '80'):
                        good_referer = '%s:%s' % (good_referer, server_port)
                else:
                    good_referer = request.get_host()

                good_hosts = list(settings.CSRF_TRUSTED_ORIGINS)
                good_hosts.append(good_referer)

                if not any(is_same_domain(referer.netloc, host) for host in good_hosts):
                    reason = REASON_BAD_REFERER % referer.geturl()
                    return self._reject(request, reason)

            csrf_token = request.META.get('CSRF_COOKIE')
            if csrf_token is None:
                return self._reject(request, REASON_NO_CSRF_COOKIE)

            request_csrf_token = ""
            if request.method == "POST":
                try:
                    request_csrf_token = request.POST.get('csrfmiddlewaretoken', '')
                except IOError:
                    pass

            if request_csrf_token == "":
                request_csrf_token = request.META.get(settings.CSRF_HEADER_NAME, '')

            request_csrf_token = _sanitize_token(request_csrf_token)
            if not _compare_salted_tokens(request_csrf_token, csrf_token):
                return self._reject(request, REASON_BAD_TOKEN)

        return self._accept(request)

    def process_response(self, request, response):
        if not getattr(request, 'csrf_cookie_needs_reset', False):
            if getattr(response, 'csrf_cookie_set', False):
                return response

        if not request.META.get("CSRF_COOKIE_USED", False):
            return response

        self._set_token(request, response)
        response.csrf_cookie_set = True
        return response
```

从上面的源码中可以看到，CsrfViewMiddleware中间件中定义了process_request，process_view和process_response三个方法

## 先来看process_request方法

```python
def _get_token(self, request):  
    if settings.CSRF_USE_SESSIONS:  
        try:  
            return request.session.get(CSRF_SESSION_KEY)  
        except AttributeError:  
            raise ImproperlyConfigured(  
                'CSRF_USE_SESSIONS is enabled, but request.session is not '  
 'set. SessionMiddleware must appear before CsrfViewMiddleware ' 'in MIDDLEWARE%s.' % ('_CLASSES' if settings.MIDDLEWARE is None else '')  
            )  
    else:  
        try:  
            cookie_token = request.COOKIES[settings.CSRF_COOKIE_NAME]  
        except KeyError:  
            return None  
  
  csrf_token = _sanitize_token(cookie_token)  
        if csrf_token != cookie_token:  
            # Cookie token needed to be replaced;  
 # the cookie needs to be reset.  request.csrf_cookie_needs_reset = True  
 return csrf_token

def process_request(self, request):  
        csrf_token = self._get_token(request)  
        if csrf_token is not None:  
            # Use same token next time.  
      request.META['CSRF_COOKIE'] = csrf_token
```

从Django项目配置文件夹中读取CSRF_USE_SESSIONS的值，如果获取成功，则从session中读取CSRF_SESSION_KEY的值，默认为'_csrftoken'，如果没有获取到CSRF_USE_SESSIONS的值，则从发送过来的请求中获取CSRF_COOKIE_NAME的值，如果没有定义则返回None。

## 再来看process_view方法

在process_view方法中，先检查视图函数是否被csrf_exempt装饰器装饰，如果视图函数没有被csrf_exempt装饰器装饰，则程序继续执行，否则返回None。接着从request请求头中或者cookie中获取携带的token并进行验证，验证通过才会继续执行与URL匹配的视图函数，否则就返回403 Forbidden错误。

实际项目中，会在发送POST,PUT,DELETE,PATCH请求时，在提交的form表单中添加

	{% csrf_token %}
	
即可，否则会出现403的错误。




