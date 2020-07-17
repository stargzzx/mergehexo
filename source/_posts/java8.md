---
title: Retrofit | 入门和简介
date: 2020-07-17 14:30:45
categories:
- [java,第三方库,Retrofit]
tags:
- Retrofit
---
项目中用 `OkHttp` 很少，用的都是封装了更高一层的 `Retrofit`。

<!-- more -->

<br/>

# 参考资料

<br/>

- [官方文档](https://square.github.io/retrofit/#introduction)
- [OkHttp与Retrofit的作用和联系](https://juejin.im/post/5d7318935188250c992d56d9)
- [Okhttp 与 Retrofit的简单介绍及两者间的联系](https://blog.csdn.net/u013132758/article/details/79690882)

<br/>

# OkHttp 与 Retrofit

<br/>

`Retrofit`与`okhttp`共同出自于`Square公司`，`Retrofit`是一个`RESTful`的`HTTP`网络请求框架，就是对`okhttp`做了一层封装。把网络请求都交给给了`Okhttp`，我们只需要通过简单的配置就能使用`retrofit`来进行网络请求了。`Retrofit框架`存在的优势：

- `Retrofit`使用注解方式，大大简化了我们的`URL`拼写形式，而且注解含义一目了然，简单易懂；
- `Retrofit`使用简单，结构层次分明，每一步都能清晰的表达出之所以要使用的寓意；
- `Retrofit`支持同步和异步执行，使得请求变得异常简单，只要调用`enqueue/execute`即可完成；
- `Retrofit`更大自由度的支持我们自定义的业务逻辑，如自定义`Converters`。
- `Retrofit`支持多种文件解析(`Gson,Json`,`XML`,`Protobuf`)
- `Retrofit`支持RxJava

`Retrofit`是基于APP发起请求的封装，也就是面向的是应用层(比如响应数据的处理和错误处理等)。

`Okhttp`是对底层网络请求的封装与优化(`socket优化`，`数据压缩`，`buffer缓存`等)。

![](/images/retiofit/0_0.jpg)