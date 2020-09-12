---
title: OkHttp | 入门 快速开始
date: 2020-07-16 23:32:02
categories:
- [java,第三方库,OkHttp]
tags:
- OkHttp
---
作为网络连接的基础库，必须要学一下。

<!-- more -->

<br/>

# OkHttp 之前的时代

<br/>

在讲述OkHttp之前, 我们看下没有OkHttp的时代, 我们是如何完成http请求的.

在没有OkHttp的日子, 我们使用

- HttpURLConnection
- HttpClient

那么这两者都有什么优缺点呢? 为什么不在继续使用下去呢?

`HttpClient`是Apache基金会的一个开源网络库, 功能十分强大, API数量众多, 但是正是由于庞大的API数量使得我们很难在不破坏兼容性的情况下对它进行升级和扩展, 所以Android团队在提升和优化HttpClient方面的工作态度并不积极.

`HttpURLConnection`是一种多用途, 轻量极的HTTP客户端, 提供的API比较简单, 可以容易地去使用和扩展. 不过在Android 2.2版本之前, `HttpURLConnection`一直存在着一些令人厌烦的bug. 比如说对一个可读的`InputStream`调用`close()`方法时，就有可能会导致连接池失效了。

<br/>

# OkHttp

<br/>

优点在这里不说了，网上一大堆。

[官方文档](https://square.github.io/okhttp/)

## 安装

使用 `gradle`

	implementation("com.squareup.okhttp3:okhttp:版本号")

## 快速入门

- [参考资料](https://www.vergil.com.cn/129.html)

## 使用方式

基本使用步骤如下

- 构建客户端对象`OkHttpClient`
- 构建请求`Request`
- 生成`Call`对象
- `Call`发起请求（同步/异步）

### GET 请求

以百度主页为例，进行Get请求：

```java
OkHttpClient httpClient = new OkHttpClient();

String url = "https://www.baidu.com/";
Request getRequest = new Request.Builder()
        .url(url)
        .get()
        .build();

Call call = httpClient.newCall(getRequest);

new Thread(new Runnable() {
    @Override
    public void run() {
        try {
            //同步请求，要放到子线程执行
            Response response = call.execute();
            Log.i(TAG, "okHttpGet run: response:"+ response.body().string());
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}).start();
```

首先，创建了`OkHttpClient`实例，接着用`Request.Builder`构建了`Request`实例并传入了百度主页的`url`，然后`httpClient.newCall`方法传入`Request`实例生成`call`，最后在子线程调用`call.execute()`执行请求获得结果`response`。

所以，使用`OkHttp`进行`get`请求，是比较简单的，只要在构建`Request`实例时更换`url`就可以了。

#### 异步请求

有个问题，你可能注意到了，这里是放在子线程执行请求的，这是因为`call.execute()`是同步方法。想要在主线程直接使用而不用手动创建子线程可以嘛？当然可以，使用`call.enqueue(callback)`即可：

```java
call.enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
    }

    @Override
    public void onResponse(Call call, Response response) throws IOException {
        Log.i(TAG, "okHttpGet enqueue: onResponse:"+ response.body().string());

        ResponseBody body = response.body();
        String string = body.string();
        byte[] bytes = body.bytes();
        InputStream inputStream = body.byteStream();
    }
});
```

`call.enqueue`会异步执行，需要注意的是，两个回调方法`onFailure`、`onResponse`是执行在子线程的，所以如果想要执行UI操作，需要使用Handler切换到UI线程。

同步的情况下，也可以是主线程中运行。

```java
import okhttp3.*;

import java.io.IOException;

public class Person {
    public static void main(String[] args) throws IOException {
        OkHttpClient httpClient = new OkHttpClient();
        String url = "https://www.baidu.com/";
        Request getRequest = new Request.Builder()
                .url(url)
                .get()
                .build();

        Call call = httpClient.newCall(getRequest);
        Response response = call.execute();
        System.out.println(response);
    }
}
```

#### 取消请求

每一个`Call`只能执行一次（原因会在下篇流程分析中说明）。 如果想要取消正在执行的请求，可以使用`call.cancel()`，通常在离开页面时都要取消执行的请求的。

#### 结果处理

请求回调的两个方法是指 传输层 的失败和成功。`onFailure`通常是`connection`连接失败或读写超时；`onResponse`是指，成功的从服务器获取到了结果，但是这个结果的响应码可能是404、500等，也可能就是200（`response.code()`的取值）。

如果`response.code()`是`200`，表示应用层请求成功了。此时我们可以获取`Response`的`ResponseBody`，这是`响应体`。从面看到，可以从`ResponseBody`获取`string`、`byte[]`、`InputStream`，这样就可以对结果进行很多操作了，比如`UI`上展示`string`（要用`Handler`切换到UI线程）、通过`InputStream`写入文件等等。

上面异步请求执行后 结果打印如下：

    2020-05-04 21:52:56.446 32681-3631/com.hfy.androidlearning I/OkHttpTestActivity: okHttpGet run: response:<!DOCTYPE html>
    <!--STATUS OK--><html> <head><meta http-equiv=content-type content=text/html;charset=utf-8><meta http-equiv=X-UA-Compatible content=IE=Edge><meta content=always name=referrer><link rel=stylesheet type=text/css href=https://ss1.bdstatic.com/5eN1bjq8AAUYm2zgoY3K/r/www/cache/bdorz/baidu.min.css><title>百度一下，你就知道</title></head> <body link=#0000cc> <div id=wrapper> <div id=head> <div class=head_wrapper> <div class=s_form> <div class=s_form_wrapper> <div id=lg> <img hidefocus=true src=//www.baidu.com/img/bd_logo1.png width=270 height=129> </div> <form id=form name=f action=//www.baidu.com/s class=fm> <input type=hidden name=bdorz_come value=1> <input type=hidden name=ie value=utf-8> <input type=hidden name=f value=8> <input type=hidden name=rsv_bp value=1> <input type=hidden name=rsv_idx value=1> <input type=hidden name=tn value=baidu><span class="bg s_ipt_wr"><input id=kw name=wd class=s_ipt value maxlength=255 autocomplete=off autofocus=autofocus></span><span class="bg s_btn_wr"><input type=submit id=su value=百度一下 class="bg s_btn" autofocus></span> </form> </div> </div> <div id=u1> <a href=http://news.baidu.com name=tj_trnews class=mnav>新闻</a> <a href=https://www.hao123.com name=tj_trhao123 class=mnav>hao123</a> <a href=http://map.baidu.com name=tj_trmap class=mnav>地图</a> <a href=http://v.baidu.com name=tj_trvideo class=mnav>视频</a> <a href=http://tieba.baidu.com name=tj_trtieba class=mnav>贴吧</a> <noscript> <a href=http://www.baidu.com/bdorz/login.gif?login&amp;tpl=mn&amp;u=http%3A%2F%2Fwww.baidu.com%2f%3fbdorz_come%3d1 name=tj_login class=lb>登录</a> </noscript> <script>document.write('<a href="http://www.baidu.com/bdorz/login.gif?login&tpl=mn&u='+ encodeURIComponent(window.location.href+ (window.location.search === "" ? "?" : "&")+ "bdorz_come=1")+ '" name="tj_login" class="lb">登录</a>');
    </script> <a href=//www.baidu.com/more/ name=tj_briicon class=bri style="display: block;">更多产品</a> </div> </div> </div> <div id=ftCon> <div id=ftConw> <p id=lh> <a href=http://home.baidu.com>关于百度</a> <a href=http://ir.baidu.com>About Baidu</a> </p> <p id=cp>&copy;2017&nbsp;Baidu&nbsp;<a href=http://www.baidu.com/duty/>使用百度前必读</a>&nbsp; <a href=http://jianyi.baidu.com/ class=cp-feedback>意见反馈</a>&nbsp;京ICP证030173号&nbsp; <img src=//www.baidu.com/img/gs.gif> </p> </div> </div> </div> </body> </html>

可见百度首页的get请求成功响应了。

### POST 请求

#### post请求提交String、文件

`post`请求与`get`请求的区别 是在构造`Request`对象时，需要多构造一个`RequestBody`对象，用它来携带我们要提交的数据，其他都是一样的。示例如下：

```java
OkHttpClient httpClient = new OkHttpClient();

MediaType contentType = MediaType.parse("text/x-markdown; charset=utf-8");
String content = "hello!";
RequestBody body = RequestBody.create(contentType, content);

Request getRequest = new Request.Builder()
        .url("https://api.github.com/markdown/raw")
        .post(body)
        .build();

Call call = httpClient.newCall(getRequest);

call.enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
    }

    @Override
    public void onResponse(Call call, Response response) throws IOException {
        Log.i(TAG, "okHttpPost enqueue: \n onResponse:"+ response.toString() +"\n body:" +response.body().string());
    }
});
```

对比`get`请求，把构建`Request`时的`get()`改成`post(body)`，并传入`RequestBody`实例。`RequestBody`实例是通过`create`方法创建，需要指定请求体内容类型、请求体内容。这里是传入了一个指定为`markdown`格式的文本。

结果打印如下：

    2020-05-05 13:18:26.445 13301-13542/com.hfy.androidlearning I/OkHttpTestActivity: okHttpPost enqueue: 
     onResponse:Response{protocol=http/1.1, code=200, message=OK, url=https://api.github.com/markdown/raw}
     body:<p>hello!</p>

请求成功并把请求体内容又返回来了。

传入`RequestBody`的 `MediaType` 还可以是其他类型，如客户端要给后台发送`json字符串`、发送一张`图片`，那么可以定义为：

```java
// RequestBody:jsonBody，json字符串
String json = "jsonString";
RequestBody jsonBody = RequestBody.create(MediaType.parse("application/json; charset=utf-8"), json);

//RequestBody:fileBody, 上传文件
File file = new File(Environment.getExternalStorageDirectory(), "1.png");
RequestBody fileBody = RequestBody.create(MediaType.parse("image/png"), file);
```

MediaType更多类型信息可以查看 [RFC 2045](https://www.vergil.com.cn/go/aHR0cHM6Ly90b29scy5pZXRmLm9yZy9odG1sL3JmYzIwNDU=)。

#### post请求提交表单
构建`RequestBody`除了上面的方式，还有它的子类`FormBody`，`FormBody`用于提交表单键值对，这种能满足平常开发大部分的需求。

```java
//RequestBody:FormBody，表单键值对
RequestBody formBody = new FormBody.Builder()
        .add("username", "hfy")
        .add("password", "qaz")
        .build();
```

`FormBody`是通过`FormBody.Builder`用构建者模式创建，`add`键值对即可。它的`contentType`在内部已经指定了。

    private static final MediaType CONTENT_TYPE = MediaType.get("application/x-www-form-urlencoded");

#### post请求提交复杂请求体

`RequestBody`另一个子类`MultipartBody`，用于`post`请求提交复杂类型的请求体。复杂请求体可以同时包含多种类型的的请求体数据。

上面介绍的 post请求 string、文件、表单，只有单一类型。考虑一种场景–注册场景，用户填写完姓名、电话，同时要上传头像图片，这时注册接口的请求体就需要 接受 表单键值对 以及文件了，那么前面讲的的post就无法满足了。那么就要用到`MultipartBody`了。 完整代码如下：

```java
OkHttpClient httpClient = new OkHttpClient();

//MediaType contentType = MediaType.parse("text/x-markdown; charset=utf-8");
//String content = "hello!";
//RequestBody body = RequestBody.create(contentType, content);

//RequestBody:fileBody,上传文件
File file = drawableToFile(this, R.mipmap.bigpic, new File("00.jpg"));
RequestBody fileBody = RequestBody.create(MediaType.parse("image/jpg"), file);


//RequestBody:multipartBody, 多类型 （用户名、密码、头像）
MultipartBody multipartBody = new MultipartBody.Builder()
        .setType(MultipartBody.FORM)
        .addFormDataPart("username", "hufeiyang")
        .addFormDataPart("phone", "123456")
        .addFormDataPart("touxiang", "00.png", fileBody)
        .build();


Request getRequest = new Request.Builder()
        .url("http://yun918.cn/study/public/file_upload.php")
        .post(multipartBody)
        .build();

Call call = httpClient.newCall(getRequest);

call.enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {

        Log.i(TAG, "okHttpPost enqueue: \n onFailure:"+ call.request().toString() +"\n body:" +call.request().body().contentType()
        +"\n IOException:"+e.getMessage());
    }

    @Override
    public void onResponse(Call call, Response response) throws IOException {
        Log.i(TAG, "okHttpPost enqueue: \n onResponse:"+ response.toString() +"\n body:" +response.body().string());
    }
});
```

可见，在构建`RequestBody`时是使用`MultipartBody.Builder`构建了`MultipartBody`实例，通过`addFormDataPart`方法传入了姓名、电话的键值对，也通过`addFormDataPart(“touxiang”, “00.png”, fileBody)`传入了头像图片，其中`"touxiang"`是`key值`， `"00.png"`是文件名，`fileBody`是要以上传的图片创建的`RequestBody`。

因为所有数据都是以键值对的表单形式提交，所以要设置`setType(MultipartBody.FORM)`。

>其他请求方式像`put`、`header`、`delete`，主要在构建`Request`时把`get()`或`post()`换成`put()`、`header()`、`delete()`就可以了，但一般在Android端很少用到。

### 请求配置项

先看几个问题：

- 如何全局设置超时时长?
- 缓存位置、最大缓存大小 呢？
- 考虑有这样一个需求，我要监控`App`通过 `OkHttp` 发出的 所有 原始请求，以及整个请求所耗费的时间，如何做？

这些问题，在`OkHttp`这里很简单。把`OkHttpClient`实例的创建，换成以下方式即可：

```java
OkHttpClient client = new OkHttpClient.Builder()
        .connectTimeout(15, TimeUnit.SECONDS)
        .readTimeout(10, TimeUnit.SECONDS)
        .writeTimeout(10, TimeUnit.SECONDS)
        .cache(new Cache(getExternalCacheDir(),500 * 1024 * 1024))
        .addInterceptor(new Interceptor() {
            @Override
            public Response intercept(Chain chain) throws IOException {
                Request request = chain.request();
                String url = request.url().toString();
                Log.i(TAG, "intercept: proceed start: url"+ url+ ", at "+System.currentTimeMillis());
                Response response = chain.proceed(request);
                ResponseBody body = response.body();
                Log.i(TAG, "intercept: proceed end: url"+ url+ ", at "+System.currentTimeMillis());
                return response;
            }
        })
        .build();
```

这里通过`OkHttpClient.Builder`通过构建者模式设置了连接、读取、写入的超时时长，用`cache()`方法传入了由缓存目录、缓存大小构成的`Cache实例`，这样就解决了前两个问题。

还注意到，使用`addInterceptor()`方法添加了`Interceptor实例`，且重写了`intercept方法`。`Interceptor`意为拦截器，`intercept()`方法会在开始执行请求时调用。其中`chain.proceed(request)`内部是真正请求的过程，是阻塞操作，执行完后会就会得到请求结果`ResponseBody`，所以`chain.proceed(request)`的前后取当前时间，那么就知道整个请求所耗费的时间。上面`chain.proceed(request)`的前后分别打印的日志和时间，这样第三个问题也解决了。

#### 一个可运行的拦截器

```java
package com.star;

import okhttp3.*;

import java.io.IOException;
import java.util.concurrent.TimeUnit;

public class Person {
    public static void main(String[] args) throws IOException {

        OkHttpClient client = new OkHttpClient.Builder()
                .connectTimeout(15, TimeUnit.SECONDS)
                .readTimeout(10, TimeUnit.SECONDS)
                .writeTimeout(10, TimeUnit.SECONDS)
                .addInterceptor(new Interceptor() {
                    @Override
                    public Response intercept(Chain chain) throws IOException {
                        Request request = chain.request();
                        String url = request.url().toString();
                        Response response = chain.proceed(request);
                        ResponseBody body = response.body();
                        return response;   // 断点 1
                    }
                })
                .build();

        String url = "https://www.baidu.com/"; // 断点 2
        Request getRequest = new Request.Builder()
                .url(url)
                .get()
                .build();

        Call call = client.newCall(getRequest);
        Response response = call.execute();
        System.out.println(response);  // 断点 3
    }
}
```

这里面有三个端点，如果经过 `debug` 的话，其顺序是这样的

    断点 2 -> 断点 1 -> 断点 3

>通常`OkHttpClient`实例是全局唯一的，这样这些基本配置就是统一，且内部维护的连接池也可以有效复用

全局配置的有了，单个请求的也可以有一些单独的配置。

```java
Request getRequest = new Request.Builder()
        .url("http://yun918.cn/study/public/file_upload.php")
        .post(multipartBody)
        .addHeader("key","value")
        .cacheControl(CacheControl.FORCE_NETWORK)
        .build();
```

这个`Request`实例.

- 使用`addHeader()`方法添加了请求头。
- 使用`cacheControl(CacheControl.FORCE_NETWORK)`设置此次请求是能使用网络，不用缓存。（还可以设置只用缓存FORCE_CACHE。）

好了，okhttp的使用就讲这里了，总体还是比较简单的。