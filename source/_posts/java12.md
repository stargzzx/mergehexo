---
title: OkHttp | 用法
date: 2020-07-23 17:20:41
categories:
- [java,第三方库,OkHttp]
tags:
- OkHttp
---
建议你优先看一下我的入门文章。

- [OkHttp | 入门](https://benpaodewoniu.github.io/2020/07/16/java5/)

<!-- more -->

## GET 异步

```java
package com.star;

import okhttp3.*;

import java.io.IOException;

public class RequsetTest {
    public static void main(String[] args) throws IOException {
        OkHttpClient client = new OkHttpClient.Builder().build();
        final Request request = new Request.Builder().url("https://github.com/").build();
        Call call = client.newCall(request);
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                System.out.println("Fail");
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                System.out.println(response.body().string());
            }
        });
        System.out.println(1);

    }
}
```

上述程序优先是 `1` 输出，然后，才是网页信息。