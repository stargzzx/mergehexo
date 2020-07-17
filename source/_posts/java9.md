---
title: OkHttp | 同步源码简易分析
date: 2020-07-17 16:21:27
categories:
- [java,第三方库,OkHttp]
tags:
- OkHttp
---
推荐你先看我之前的文章。

- [OkHttp | 入门](https://benpaodewoniu.github.io/2020/07/16/java5/)

<!-- more -->

<br/>

# 参考资料

<br/>

- [OkHttp 同步请求 源码分析](https://www.jianshu.com/p/be34053324a1)

<br/>

# 简单的例子

<br/>

```java
package com.star;

import okhttp3.Headers;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;

import java.io.IOException;

public class RequsetTest {
    public static void main(String[] args) throws IOException {
        OkHttpClient client = new OkHttpClient.Builder().build();
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

<br/>

# 源码分析

<br/>

## 解读 OkHttpClient

我们从

	OkHttpClient client = new OkHttpClient.Builder().build();

通过查看源码我们知道了创建 OkHttpClient 对象，实际上是 `OkHttpClient` 中静态类 `Builder` 的 `Builder()` 的构造方法先初始化了一些参数和对象(这些参数对象会在后面的请求流程中会用到)

### OkHttpClient.Builder()

```java
public Builder() {
  dispatcher = new Dispatcher();
  protocols = DEFAULT_PROTOCOLS;
  connectionSpecs = DEFAULT_CONNECTION_SPECS;
  eventListenerFactory = EventListener.factory(EventListener.NONE);
  proxySelector = ProxySelector.getDefault();
  if (proxySelector == null) {
    proxySelector = new NullProxySelector();
  }
  cookieJar = CookieJar.NO_COOKIES;
  socketFactory = SocketFactory.getDefault();
  hostnameVerifier = OkHostnameVerifier.INSTANCE;
  certificatePinner = CertificatePinner.DEFAULT;
  proxyAuthenticator = Authenticator.NONE;
  authenticator = Authenticator.NONE;
  connectionPool = new ConnectionPool();
  dns = Dns.SYSTEM;
  followSslRedirects = true;
  followRedirects = true;
  retryOnConnectionFailure = true;
  callTimeout = 0;
  connectTimeout = 10_000;
  readTimeout = 10_000;
  writeTimeout = 10_000;
  pingInterval = 0;
}
```

再通过调用 `OkHttpClient` 自己的 `build()` 方法将初始化好了的 `Builder` 对象赋值给本身，从而创建出了一个客服端的 `OkHttpClient` 对象。

#### OkHttpClient.Builder().build()

```java
public OkHttpClient build() {
  return new OkHttpClient(this);
}
```

当我们创建一个对象时，如果这个对象需要很多参数，这个时候我们也可以用到`Builder`这种创建模式。

用 `Builder` 对象来封装我们初始化对象所需要的参数，然后传递这个`Builder`对象到初始化对象构造方法里，完成整个初始化对象属性的初始化。

## 解读 Request

	final Request request = new Request.Builder().url("https://github.com/").build();

查看源码我们知道了创建 `Request` 对象，实际上也是 `Request` 中静态类 `Builder` 的 `Builder()` 的构造方法先初始化了一些参数，默认了请求方式为 `get`

### Request.Builder()

```java
public Builder() {
  this.method = "GET";
  this.headers = new Headers.Builder();
}
```

#### Request.Builder().url().build()

```java
public Request build() {
  if (url == null) throw new IllegalStateException("url == null");
  return new Request(this);
}
```

可以发现 `OkHttpClient` 和 `Request` 都是用的Builder这种创建模式

## 创建 Call 对象

	Response response = client.newCall(request).execute();

实际上是通过创建好了的 `OkHttpClient` 对象调用 `newCall(Request request)` 方法来封装创建好的 `Request` 对象，从而得到 `Call` 对象。

`Call` 通过查看源码发现 `Call` 只是一个接口，再从 `newCall()` 去查看源码，由于 `Call` 是一个接口，所以 `Call` 的实际操作都是在 `Call` 的实现类 `RealCall` 中所做的。

```java
@Override public Call newCall(Request request) {
	return RealCall.newRealCall(this, request, false /* for web socket */);
}
```

如果直接从 `client.newCall(request).execute();` 最后一个方法 `execute()` 可以看出，最开始这个是一个接口。

```java
public interface Call extends Cloneable {
  Request request();
  Response execute() throws IOException;
  ...
```

所以，我们要看 `newCall` 因为这个类 `implements` 了 `Call` ，所以，要查看实现类是怎么写的。

### client.newCall(request)

```java
@Override public Call newCall(Request request) {
   return RealCall.newRealCall(this, request, false /* for web socket */);
 }
 ```

`OkHttp`中不管是同步请求还是异步请求，都是通过 `OkHttpClient` 对象调用 `newCall(Request request)` 方法来进行创建的，然后再通过创建好的 `Call` 对象来进行相应的操作。

#### client.newCall(request).execute()

```java
@Override public Response execute() throws IOException {
    synchronized (this) {
      if (executed) throw new IllegalStateException("Already Executed");
      executed = true;
    }
    transmitter.timeoutEnter();
    transmitter.callStart();
    try {
      client.dispatcher().executed(this);
      return getResponseWithInterceptorChain();
    } finally {
      client.dispatcher().finished(this);
    }
  }
```


ps: 后面我就没有继续分析，如果想看后续，请移步参考资料
