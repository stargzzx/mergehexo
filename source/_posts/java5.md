---
title: OkHttp | 入门
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

## 快速入门「同步」

- [参考资料](https://www.jianshu.com/p/c96a43745a8f)

### 简单的 demo

```java
package com.star;

import okhttp3.Headers;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

import java.io.IOException;

public class RequsetTest {
    public static void main(String[] args) throws IOException {
        OkHttpClient client = new OkHttpClient();
        final Request request = new Request.Builder().url("https://github.com/").build();
        Response response = client.newCall(request).execute();
        if (!response.isSuccessful()) {
            throw new IOException("Unexpected code " + response);
        }
        Headers responseHeaders = response.headers();
        for (int i = 0; i < responseHeaders.size(); i++) {
            System.out.println(responseHeaders.name(i) + ": " + responseHeaders.value(i));
        }

        System.out.println(response.body().string());
    }
}
```

### 第一步：创建 OkHttpClient 对象

`OkHttpClient` 代表是客服端类。

两种创建方式

#### 默认的

直接 `new OkHttpClient()` 对象

    OkHttpClient client = new OkHttpClient(); 

#### 参数实例化

考虑到实际开发中网络情况是比较复杂的，在创建的时候就需要设置一些参数，就需要通过 `OkHttpClient` 内部类 `Builder` 的 `Builder()` 构造方法实例化一些参数，进行参数的设置。

```java
okHttpClient=new OkHttpClient.Builder()
                .connectTimeout(CONNECT_TIMEOUT, TimeUnit.SECONDS)
                .readTimeout(IO_TIMEOUT, TimeUnit.SECONDS)
                .writeTimeout(IO_TIMEOUT, TimeUnit.SECONDS)
                .addNetworkInterceptor(new StethoInterceptor())//google Chrome 查看请求信息 日志
                .addInterceptor(new RequestInterceptor())//请求头
                .build();
```

### 第二步：同样以Builder的模式创建了 Request 对象

Request 就是我们发送请求的报文信息,包含了

- 请求的 `Url` 地址
- 请求的方法 `method`（`get`，`post`...等等)
- 请求头 `Header`
- 请求参数 `RequestBody`

...等等

```java
Request request = new Request.Builder()
                .url("www.baidu.com")
                .get().build();
```

### 第三步：创建 Call 对象

将创建的`request`封装成了`Call`对象。

就会想到什么是 `Call 对象`，我们可以把 `Call`当作连接 `Request` 和 `Response` 连接的桥梁.

我们通过 `OkHttpClient` 的 `newCall(Request request)` 方法并把创建好的 `request` 作为参数传进去，从而创建好我们的 `call 对象` 。

    Call call=client.newCall(request);

### 第四步：通过 call 对象来进行同步或者异步的调用

调用 `Call` 的 `execute()` 发送同步请求 。

这其实是同步请求和异步请求的分水岭，同步和异步前三步其实都是一样的。但是在第四部通过 `call` 来进行同步和异步的方法调用就不一样了。

```java
try {
    Response response = call.execute();
} catch (IOException e) {
    e.printStackTrace();
}
```

我们通过 `call.execute();` 来实现 `OkHttp` 的同步请求。 完成 `Response` 相应报文的读取。

`Response` 包含了状态码，相应头，响应信息，相应体...等等。

<br/>

# OkHttp同步需要注意

<br/>

发送请求后，就会进入阻塞状态，直到收到响应为止。

当前线程如果发送了同步请求之后，它就会进入阻塞，直到数据有响应为止，它才会停止阻塞。