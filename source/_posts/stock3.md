---
title: 富途 | java 对接
date: 2020-07-31 10:23:56
categories:
- [股票,futu]
tags:
- 富途
---
非常想吐槽富途的 java 文档，写的那叫一个天书，不慢慢揣摩根本不知道意思。

<!-- more -->

在这里我先假设你已经安装了富途的中间接口层，然后知道 python 对接的原理。如果你不知道，你可以看我下面的博文。

- [股票 | 富途接口对接](https://benpaodewoniu.github.io/2020/04/01/stock1/)

## 环境配置

首先要下载 java 的例子和各种包。

![](/images/stock/2_0.png)

下载下来的包解压后，里面有几个文件夹和文件

其中 `FTAPI4J` 是 java 的源代码，点进去

里面有一个 `lib` 文件，这里面放着各种需要的包。

- ftpi4j.jar 这个是富途官方提供的包

这个包，我们需要添加到项目中，如果你是 `gradle` 项目，并且假如你不会添加第三方包，可以看我下面的博文。

- [gradle | 加载项目中的本地jar文件](https://benpaodewoniu.github.io/2020/07/15/gradle0/)

其他 `jar` 包都是依赖，如果你是 `gradle` 你可以使用下面的语法来引入相关的依赖包。

	compile group: 'com.google.protobuf', name: 'protobuf-java', version: '3.5.1'
	compile group: 'net.java.dev.jna', name: 'jna', version: '5.3.1'
	compile group: 'net.java.dev.jna', name: 'jna-platform', version: '5.3.1'

另外，我们还看到有各种平台的文件夹，比如

- darwin「这个对应于 MacBook」
- ubuntu

等，这个是，平台编译的相关的文件，我们根据不同的平台，将相关的文件放置在项目的根目录下。

另外，比较重要的一点是，`sample` 文件夹里面存着官方给的例子。

在这里我们不直接看 `sample` 代码，而是，看一下官方文档中给的例子。

## 快速上手

- [快速上手](https://futunnopen.github.io/futu-api-doc/java/start.html)

如果我们直接按照上面的环境配好，并且，将上面的代码都写好

```python
package com.star;

import com.futu.openapi.*;
import com.futu.openapi.pb.*;

public class FutuStart {
    public static void main(String[] args) {
        FTAPI.init(); //初始化环境
        FTAPI_Conn_Qot client = new FTAPI_Conn_Qot(); //创建行情对象
        client.setConnSpi(new SampleQotCallback()); //创建连接回调类
        client.setQotSpi(new SampleQotCallback()); //创建行情数据回调类
        client.setClientInfo("FTAPI4J_Sample", 1); //建立标识
        client.initConnect("127.0.0.1", (short)11111, false); //开始连接
    }
}


class SampleQotCallback implements FTSPI_Qot, FTSPI_Conn
{
    @Override
    public void onInitConnect(FTAPI_Conn client, long errCode, String desc)
    {
        System.out.printf("Qot onInitConnect: ret=%b desc=%s connID=%d\n", errCode, desc, client.getConnectID());
        //简单演示一下获取用户行情基本信息
        FTAPI_Conn_Qot qot = (FTAPI_Conn_Qot)client;
        {
            GetGlobalState.Request req = GetGlobalState.Request.newBuilder().setC2S(
                    GetGlobalState.C2S.newBuilder().setUserID(15891586)
            ).build();
            int seqNo = qot.getGlobalState(req);
            System.out.printf("Send GetGlobalState: %d\n", seqNo);
        }
    }

    @Override
    public void onDisconnect(FTAPI_Conn client, long errCode) {
        System.out.printf("Qot onDisConnect: %d\n", errCode);
    }

    @Override
    public void onReply_GetGlobalState(FTAPI_Conn client, int nSerialNo, GetGlobalState.Response rsp) {
        System.out.printf("Reply: GetGlobalState: %d  %s\n", nSerialNo, rsp.toString());
    }
}
```

当你运行之后，会发现什么也没有输出，这是因为，我们必须给源代码加上 `sleep`。

改成

```python
package com.star;

import com.futu.openapi.*;
import com.futu.openapi.pb.*;

public class FutuStart {
    public static void main(String[] args) throws InterruptedException {
        FTAPI.init(); //初始化环境
        FTAPI_Conn_Qot client = new FTAPI_Conn_Qot(); //创建行情对象
        client.setConnSpi(new SampleQotCallback()); //创建连接回调类
        client.setQotSpi(new SampleQotCallback()); //创建行情数据回调类
        client.setClientInfo("FTAPI4J_Sample", 1); //建立标识
        client.initConnect("127.0.0.1", (short)11111, false); //开始连接
        Thread.sleep(1000 * 600);
    }
}


class SampleQotCallback implements FTSPI_Qot, FTSPI_Conn
{
    @Override
    public void onInitConnect(FTAPI_Conn client, long errCode, String desc)
    {
        System.out.printf("Qot onInitConnect: ret=%b desc=%s connID=%d\n", errCode, desc, client.getConnectID());
        //简单演示一下获取用户行情基本信息
        FTAPI_Conn_Qot qot = (FTAPI_Conn_Qot)client;
        {
            GetGlobalState.Request req = GetGlobalState.Request.newBuilder().setC2S(
                    GetGlobalState.C2S.newBuilder().setUserID(15891586)
            ).build();
            int seqNo = qot.getGlobalState(req);
            System.out.printf("Send GetGlobalState: %d\n", seqNo);
        }
    }

    @Override
	public void onInitConnect(FTAPI_Conn client, long errCode, String desc) {
	    System.out.printf("Qot connect");
	}

    @Override
    public void onDisconnect(FTAPI_Conn client, long errCode) {
        System.out.printf("Qot onDisConnect: %d\n", errCode);
    }

    @Override
    public void onReply_GetGlobalState(FTAPI_Conn client, int nSerialNo, GetGlobalState.Response rsp) {
        System.out.printf("Reply: GetGlobalState: %d  %s\n", nSerialNo, rsp.toString());
    }
}
```

就会发现有输出了。

在这里我讲一下 `java` 的代码编写逻辑。

首先，`java` 有两个暴漏接口「在这里只针对行情，对下单不做分析」

- FTSPI_Qot
- FTSPI_Conn

我们用一个类组合上面的两个接口，然后，会重写回调函数。

比如，如果

	client.initConnect("127.0.0.1", (short)11111, false); //开始连接

调用成功后，会回调

	@Override
	public void onInitConnect(FTAPI_Conn client, long errCode, String desc) {
	    你写的代码
	}

再比如，上面的调用

	qot.getGlobalState(req);

如果调用成功，会回调

	@Override
	public void onReply_GetGlobalState(FTAPI_Conn client, int nSerialNo, GetGlobalState.Response rsp) {
	    System.out.printf("Reply: GetGlobalState: %d  %s\n", nSerialNo, rsp.toString());
	}

那如何知道怎么调用接口，以及回调函数。

这个时候，没有啥好办法，只能看文档和 `sample` 中的例子。

- [行情API](https://futunnopen.github.io/futu-api-doc/java/Quote_API.html)

不得不说，他们的文档写的非常烂，所以，只能结合 `sample` 中的例子看。


## 对接 websocket

另外，这这里，我贴一下如何对接 `websocket` 的代码，供大家参考。

```python
package com.star;

import com.futu.openapi.*;
import com.futu.openapi.pb.*;

public class Futu {
    public static void main(String[] args) throws InterruptedException {
        FTAPI.init(); //初始化环境
        SampleQotCallback_ qot = new SampleQotCallback_();
        while (true) {
            Thread.sleep(1000 * 600);
        }
    }
}


class SampleQotCallback_ implements FTSPI_Qot, FTSPI_Conn {
    FTAPI_Conn_Qot client = new FTAPI_Conn_Qot(); //创建行情对象

    public SampleQotCallback_() {
        client.setConnSpi(this); //创建连接回调类
        client.setQotSpi(this); //创建行情数据回调类
        client.setClientInfo("FTAPI4J_Sample", 1); //建立标识
        client.initConnect("127.0.0.1", (short) 11111, false); //开始连接
    }

    @Override
    public void onInitConnect(FTAPI_Conn client, long errCode, String desc) {
        this.sub();
    }

    @Override
    public void onPush_UpdateOrderBook(FTAPI_Conn client, QotUpdateOrderBook.Response rsp){
        System.out.printf("onPush_UpdateOrderBook: %s\n", rsp.toString());
    }

    //测试订阅行情
    void sub() {
        QotCommon.Security sec = QotCommon.Security.newBuilder().setCode("00700")
                .setMarket(QotCommon.QotMarket.QotMarket_HK_Security_VALUE)
                .build();
        QotSub.C2S c2s = QotSub.C2S.newBuilder().addSecurityList(sec)
                .addSubTypeList(QotCommon.SubType.SubType_OrderBook_VALUE)
                .setIsSubOrUnSub(true)
                .setIsRegOrUnRegPush(true)
                .setIsFirstPush(true)
                .build();
        QotSub.Request req = QotSub.Request.newBuilder().setC2S(c2s).build();
        client.sub(req);
    }
}
```

## api 如何看

`futu` java 文档，写得比狗屎还烂。

属于那种感动自己的文档，就是自己内部技术人员逻辑清晰，讲起来头头是道，但是，如果没经过内部人的讲解，直接看的话，比狗屎还烂，读起来非常恶心。

我们以其中一个接口，获取某一板块下，所有股票的信息为例。

![](/images/stock/2_1.png)

其中，对于 java 而言，一个完整的请求包括

- 请求 request
    - 包含一个 C2S
        - 该例是 Qot_GetStaticInfo
- 回复 response
    - 内容包含在 S2C

### request

上面的请求

```java
message Request
{
        required C2S c2s = 1;
}
```

也就是里面包含 C2S。

这个 C2S 的内容如下

```java
message C2S
{
        optional int32 market = 1; //Qot_Common.QotMarket,股票市场
        optional int32 secType = 2; //Qot_Common.SecurityType,股票类型
        repeated Qot_Common.Security securityList = 3; //股票，若该字段存在，忽略其他字段，只返回该字段股票的静态信息
        //当传入程序无法识别的股票时（包括很久之前退市的股票和不存在的股票），仍然返回股票信息，用静态信息标志来该股票不存在。
}
```

这个意思就是这个 C2S 的类是

    Qot_GetStaticInfo

至于为什么是这个，纯粹是我猜的，因为，这个包名，也就是上面 `package Qot_GetStaticInfo` ，一般这个 api 都是这个套路。你也可以用 IDEA 自动补全来检测。没啥好办法。

下面就是 `QotGetStaticInfo` 如何设置参数，里面有两个要求

    optional int32 market = 1; //Qot_Common.QotMarket,股票市场
    optional int32 secType = 2; //Qot_Common.SecurityType,股票类型
    第三个不需要管

所以，上面的这个 C2S 的写法应该是如下

```java
QotGetStaticInfo.C2S.newBuilder().setMarket(QotCommon.QotMarket.QotMarket_HK_Security_VALUE)
                .setSecType(QotCommon.SecurityType.SecurityType_Eqty_VALUE)
```

这个时候，你可能就要问了

    optional int32 market = 1; //Qot_Common.QotMarket,股票市场
    optional int32 secType = 2; //Qot_Common.SecurityType,股票类型

我怎么知道这里面的含义代表啥意思，所以，你可以点击图片中的注解看一下。

![](/images/stock/2_2.png)

然后，重写一下回调函数。

至此，一个完整的请求，就完成了。
