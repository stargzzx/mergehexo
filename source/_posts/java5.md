---
title: java | OkHttp 入门
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

## GET

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


