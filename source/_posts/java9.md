---
title: OkHttp | 入门（二） OkHttp的工作流程分析
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

- [OkHttp 同步请求 源码分析](https://www.vergil.com.cn/132.html)

如果我们想要进行get请求，那么使用少量的代码就能实现，如下所示：

```java
OkHttpClient httpClient = new OkHttpClient();
String url = "https://www.baidu.com/";
Request getRequest = new Request.Builder()
        .url(url)
        .get()
        .build();
Call call = httpClient.newCall(getRequest);
call.enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {

    }
    @Override
    public void onResponse(Call call, Response response) throws IOException {

    }
});        
```
看起来很简洁的代码，实际是`OkHttp`在背后帮我们默默执行了成吨的工作。

知其然也要知其所以然。那么这篇我们就来解析一下`OkHttp`的源码，看看它在这些简单用法的背后，到底执行了多么复杂的工作。

<br/>

# 请求的创建

<br/>

以上面`get`请求的代码步骤分析，那么先分析`OkHttpClient`实例的创建。

在入门篇（一）中，提到`OkHttpClient实例化可以直接创建`，也可以使用`Builder`建造者模式进行配置然后`build()`即可。直接创建其实就是使用了默认的配置。其构造方法如下：

```java
  public OkHttpClient() {
    this(new Builder());
  }

  OkHttpClient(Builder builder) {
    this.dispatcher = builder.dispatcher;//调度器，用于控制并发的请求。内部保存同步和异步请求的call，并使用线程池处理异步请求。
    this.proxy = builder.proxy;//代理设置
    this.protocols = builder.protocols;//http协议
    this.connectionSpecs = builder.connectionSpecs;//连接配置
    this.interceptors = Util.immutableList(builder.interceptors);//全局拦截器
    this.networkInterceptors = Util.immutableList(builder.networkInterceptors);//网络拦截器
    this.eventListenerFactory = builder.eventListenerFactory;//请求的监听器的创建工厂
    this.proxySelector = builder.proxySelector;//代理选择器
    this.cookieJar = builder.cookieJar;//cookie,默认没有cookie：CookieJar.NO_COOKIES
    this.cache = builder.cache;//网络缓存设置
    this.internalCache = builder.internalCache;//内部使用的缓存接口
    this.socketFactory = builder.socketFactory;//socket工厂

    boolean isTLS = false;
    for (ConnectionSpec spec : connectionSpecs) {
      isTLS = isTLS || spec.isTls();
    }

    if (builder.sslSocketFactory != null || !isTLS) {
      this.sslSocketFactory = builder.sslSocketFactory;
      this.certificateChainCleaner = builder.certificateChainCleaner;
    } else {
      X509TrustManager trustManager = Util.platformTrustManager();
      this.sslSocketFactory = newSslSocketFactory(trustManager);
      this.certificateChainCleaner = CertificateChainCleaner.get(trustManager);
    }

    if (sslSocketFactory != null) {
      Platform.get().configureSslSocketFactory(sslSocketFactory);
    }

    this.hostnameVerifier = builder.hostnameVerifier;
    this.certificatePinner = builder.certificatePinner.withCertificateChainCleaner(
        certificateChainCleaner);
    this.proxyAuthenticator = builder.proxyAuthenticator;
    this.authenticator = builder.authenticator;//以上几个是安全相关设置
    this.connectionPool = builder.connectionPool;//连接池
    this.dns = builder.dns;//域名解析系统
    this.followSslRedirects = builder.followSslRedirects;//ssl重定向
    this.followRedirects = builder.followRedirects;//重定向
    this.retryOnConnectionFailure = builder.retryOnConnectionFailure;//连接失败时是否重试
    this.callTimeout = builder.callTimeout;
    this.connectTimeout = builder.connectTimeout;
    this.readTimeout = builder.readTimeout;
    this.writeTimeout = builder.writeTimeout;//几个超时设置
    this.pingInterval = builder.pingInterval;//ping间隔时间
    //拦截器不能是null
    if (interceptors.contains(null)) {
      throw new IllegalStateException("Null interceptor: " + interceptors);
    }
    if (networkInterceptors.contains(null)) {
      throw new IllegalStateException("Null network interceptor: " + networkInterceptors);
    }
  }

  public static final class Builder {
...
    public Builder() {
    //默认的配置
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
...
  }
```

直接创建`OkHttpClient实例`，配置项就是`Builder`构造方法中默认值。 可见配置项是非常多的，包括上一篇中已经使用过的超时设置、拦截器。

接着看`Request`的创建，也是使用建造者模式：

```java
public Builder() {
  this.method = "GET";
  this.headers = new Headers.Builder();
}

public Builder get() {
  return method("GET", null);
}

public Builder post(RequestBody body) {
  return method("POST", body);
}

public Builder method(String method, @Nullable RequestBody body) {
  if (method == null) throw new NullPointerException("method == null");
  if (method.length() == 0) throw new IllegalArgumentException("method.length() == 0");
  if (body != null && !HttpMethod.permitsRequestBody(method)) {
    throw new IllegalArgumentException("method " + method + " must not have a request body.");
  }
  if (body == null && HttpMethod.requiresRequestBody(method)) {
    throw new IllegalArgumentException("method " + method + " must have a request body.");
  }
  this.method = method;
  this.body = body;
  return this;
}

public Builder addHeader(String name, String value) {
  headers.add(name, value);
  return this;
}

public Builder url(String url) {
  if (url == null) throw new NullPointerException("url == null");
  // Silently replace web socket URLs with HTTP URLs.
  if (url.regionMatches(true, 0, "ws:", 0, 3)) {
    url = "http:" + url.substring(3);
  } else if (url.regionMatches(true, 0, "wss:", 0, 4)) {
    url = "https:" + url.substring(4);
  }
  return url(HttpUrl.get(url));
}

public Request build() {
  if (url == null) throw new IllegalStateException("url == null");
  return new Request(this);
}
```

注意到`get()`和`post(RequestBody body)`都是对`method`方法的包装，`method`方法内部对请求方式和请求体进行了校验，比如`get`请求不能有请求体、`post`请求必须要请求体等。其他比较好理解，不再赘述。

接着看`HttpClient`的`newCall`方法：

```java
@Override public Call newCall(Request request) {
	return RealCall.newRealCall(this, request, false /* for web socket */);
}
```

跟进`RealCall`的`newRealCall`方法：

```java
static RealCall newRealCall(OkHttpClient client, Request originalRequest, boolean forWebSocket) {
	// Safely publish the Call instance to the EventListener.
	RealCall call = new RealCall(client, originalRequest, forWebSocket);
	call.transmitter = new Transmitter(client, call);
	return call;
}
```

可见`HttpClient`的`newCall`方法获得`Call`实际是`RealCall`。`RealCall`就是准备执行的请求，是对接口`Call`的实现。其内部持有`OkHttpClient`实例、`Request`实例。并且这里还创建了`Transmitter`给`RealCall`的`transmitter`赋值。

`Transmitter`意为发射器，是应用层和网络层的桥梁，在进行 连接、真正发出请求和读取响应中起到很重要的作用，看下构造方法：

```java
public Transmitter(OkHttpClient client, Call call) {
	this.client = client;
	this.connectionPool = Internal.instance.realConnectionPool(client.connectionPool());
	this.call = call;
	this.eventListener = client.eventListenerFactory().create(call);
	this.timeout.timeout(client.callTimeoutMillis(), MILLISECONDS);
}
```

`Transmitter`内部持有`OkHttpClient`、`连接池`、`call`、`事件监听器`。

再回头看`RealCall`实现的接口`Call`：

```java
//  已准备要执行的请求。由于表示单个请求/响应对（流），因此无法执行两次
public interface Call extends Cloneable {
  ...
  //同步请求，会阻塞
  Response execute() throws IOException;

  //异步请求
  void enqueue(Callback responseCallback);

  //取消请求，已经完成的不能取消。
  void cancel();

  boolean isExecuted();
  boolean isCanceled();
  Timeout timeout();
...
  interface Factory {
    Call newCall(Request request);
  }
}
```

主要是定义请求的执行动作和状态。`RealCall`对`Call`的具体实现，在后面执行流程中说明。

好了，请求的创建就到这里了。

<br/>

# 请求的调度

<br/>

执行分为`同步`和`异步`，这里先从 同步请求 开始分析，即`RealCall`的`execute`方法：

```java
@Override public Response execute() throws IOException {
    synchronized (this) {
      if (executed) throw new IllegalStateException("Already Executed");
      executed = true;
    }
    transmitter.timeoutEnter();//超时计时开始
    transmitter.callStart();//回调请求监听器的请求开始
    try {
      client.dispatcher().executed(this);//放入队列
      return getResponseWithInterceptorChain();//执行请求获取结果
    } finally {
      client.dispatcher().finished(this);//请求结束
    }
  }
```

首先判断 如果已经执行，就会抛出异常。这就是一个请求只能执行一次的原因。然后回调请求监听器的请求开始。然后调用`client`的调度器`Dispatcher`的`executed`方法：

```java
synchronized void executed(RealCall call) {
	runningSyncCalls.add(call);
}
```

很简单，请求放入一个`双端队列runningSyncCalls`中，表示正在执行的同步请求。

然后返回了`getResponseWithInterceptorChain()`的结果`Response`，可以猜到，同步请求真正的请求流程是在`getResponseWithInterceptorChain`方法中。

最后请求结束，会走`Dispatcher`的`finished(Deque calls, T call)`方法：

```java
//结束 异步请求
void finished(AsyncCall call) {
	//callsPerHost-1
	call.callsPerHost().decrementAndGet();
	finished(runningAsyncCalls, call);
}
//结束 同步请求
void finished(RealCall call) {
	finished(runningSyncCalls, call);
}
//异步、同步的结束，都会走到这里：从running中移除 并 调用promoteAndExecute
private <T> void finished(Deque<T> calls, T call) {
	...
	synchronized (this) {
	  //从队列中移除
	  if (!calls.remove(call)) throw new AssertionError("Call wasn't in-flight!");
	  idleCallback = this.idleCallback;
	}

	boolean isRunning = promoteAndExecute();
	...

}
```

从队列中移除`call`，然后执行了 `promoteAndExecute()`，这里先不跟进去了后面会讲到。

到这里，我们知道了，同步请求走的是`getResponseWithInterceptorChain()`方法；

我们再来看 `异步请求`，即`RealCall`的`enqueue`方法：

```java
public void enqueue(Callback responseCallback) {
	synchronized (this) {
	  if (executed) throw new IllegalStateException("Already Executed");
	  executed = true;
	}
	transmitter.callStart();//回调请求监听器的请求开始
client.dispatcher().enqueue(new AsyncCall(responseCallback));//请求调度
}
```

同样先判断是否已请求过，回调请求开始。然后调用`Dispatcher`的`enqueue`方法，参数接受的是`AsyncCall`，`AsyncCall`继承`NamedRunnable`，`NamedRunnable`实现自`Runnable`，即`AsyncCall`就是个`Runnable`，可以想到它是会在`线程`或`线程池`执行`run`方法的。`run`方法在`AsyncCall`没看到啊，实际是在`NamedRunnable`中：

```java
//知道当前线程名字的Runnable
public abstract class NamedRunnable implements Runnable {
  protected final String name;

  public NamedRunnable(String format, Object... args) {
    this.name = Util.format(format, args);
  }

  public final void run() {
    String oldName = Thread.currentThread().getName();
    Thread.currentThread().setName(name);
    try {
      execute();
    } finally {
      Thread.currentThread().setName(oldName);
    }
  }

  protected abstract void execute();
}
```

`run`调用 抽象方法`execute()`，`execute()`在`AsyncCall`是有实现的，这里先不看。

我们继续去看`Dispatcher`的`enqueue`方法：

```java
void enqueue(AsyncCall call) {
	synchronized (this) {
	  //存入等待执行的队列
	  readyAsyncCalls.add(call);

	  // 相同host的请求，共用一个 调用计数
	  if (!call.get().forWebSocket) {
	    AsyncCall existingCall = findExistingCallWithHost(call.host());
	    if (existingCall != null) call.reuseCallsPerHostFrom(existingCall);
	  }
	}
	promoteAndExecute();
}

//从 runningAsyncCalls或者readyAsyncCalls中找到相同host的请求
private AsyncCall findExistingCallWithHost(String host) {
	for (AsyncCall existingCall : runningAsyncCalls) {
	  if (existingCall.host().equals(host)) return existingCall;
	}
	for (AsyncCall existingCall : readyAsyncCalls) {
	  if (existingCall.host().equals(host)) return existingCall;
	}
	return null;
}
```

先把请求放入`双端队列readyAsyncCalls`中，表示等待执行的异步请求。为啥是等待执行呢？先留一个疑问。 接着从 正在执行的请求`runningAsyncCalls` 或 等待执行的请求`readyAsyncCalls` 中找到是相同`host`的请求，把`callsPerHost`重用给当前请求。`callsPerHost`看名字感觉像是 拥有相同`host`的请求的数量，并且注意到类型是`AtomicInteger`，声明如下：

```java
private volatile AtomicInteger callsPerHost = new AtomicInteger(0);
```

所以，相同`host`的请求是共享`callsPerHost`的，为了后面判断`host`并发做准备。

继续看，接着调用了`promoteAndExecute()`，前面看的`finish`方法也有调用，这里可以跟进看看了：

```java
//调度的核心方法：在 控制异步并发 的策略基础上，使用线程池 执行异步请求
private boolean promoteAndExecute() {
	assert (!Thread.holdsLock(this));

	List<AsyncCall> executableCalls = new ArrayList<>();
	boolean isRunning;
	synchronized (this) {
	  for (Iterator<AsyncCall> i = readyAsyncCalls.iterator(); i.hasNext(); ) {
	    AsyncCall asyncCall = i.next();

	    if (runningAsyncCalls.size() >= maxRequests) break; //最大并发数64
	    if (asyncCall.callsPerHost().get() >= maxRequestsPerHost) continue; //Host最大并发数5

	    i.remove();//从等待队列中移除
	    asyncCall.callsPerHost().incrementAndGet();//Host并发数+1
	    executableCalls.add(asyncCall);//加入 可执行请求 的集合
	    runningAsyncCalls.add(asyncCall);//加入 正在执行的异步请求队列
	  }
	  isRunning = runningCallsCount() > 0;//正在执行的异步/同步 请求数 >0
	}

	for (int i = 0, size = executableCalls.size(); i < size; i++) {
	  AsyncCall asyncCall = executableCalls.get(i);
	  asyncCall.executeOn(executorService());//可执行的请求
	}

	return isRunning;
}

public synchronized int runningCallsCount() {
	return runningAsyncCalls.size() + runningSyncCalls.size();
}
```

遍历`readyAsyncCalls`，先进行两个检查：

- 正在执行的异步请求`runningAsyncCalls`数量大于最大并发请求数`64`就`break`，或者 相同`host`请求的数量大于`5`，就`continue`。
- 如果检查都通过，就从等待队列中移除，`callsPerHost`自增`1`，放入 可执行的集合`executableCalls`，并添加到队列`runningAsyncCalls`中，表示正在执行的异步请求。

这里就解释了 异步请求等待队列的意义了，就是为了控制最大并发数的缓冲：异步请求并发数达到`64`、相同`host`的异步请求达到`5`，都要放入等待队列。

遍历完后 把`executableCalls`中的请求都走`executeOn`方法：

```java
void executeOn(ExecutorService executorService) {
  assert (!Thread.holdsLock(client.dispatcher()));
  boolean success = false;
  try {
    executorService.execute(this);//在线程池执行asyncCall
    success = true;
  } catch (RejectedExecutionException e) {
    ...
    transmitter.noMoreExchanges(ioException);
    responseCallback.onFailure(RealCall.this, ioException);//回调失败
  } finally {
    if (!success) {
      client.dispatcher().finished(this); //执行发生异常，结束
    }
  }
}

//线程池的定义
public synchronized ExecutorService executorService() {
	if (executorService == null) {
	  executorService = new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60, TimeUnit.SECONDS,
	      new SynchronousQueue<>(), Util.threadFactory("OkHttp Dispatcher", false));
	}
	return executorService;
}
```

`executeOn`方法很简单：使用类似`CachedThreadPool`的线程池 执行请求`RealCall`。如果执行失败，也会调用`dispatcher`的`finished(Deque calls, T call)`方法。

前面分析过，`AsyncCall`的`run`方法会走到`execute()`方法，来看下：

```java
protected void execute() {
  boolean signalledCallback = false;
  transmitter.timeoutEnter();//超时计时开始
  try {
    Response response = getResponseWithInterceptorChain();////执行请求获取结果
    responseCallback.onResponse(RealCall.this, response);//回调结果
  } catch (IOException e) {
    ...
    responseCallback.onFailure(RealCall.this, canceledException);//回调失败
    ...
  } finally {
    client.dispatcher().finished(this);//请求结束
  }
}
```

我们发现，这里和 同步请求 就很像了，同样是调用`getResponseWithInterceptorChain()`方法来获取结果`Response`，不同点是使用`responseCallback`把结果回调出去，最后请求结束也是调用了`dispatcher`的`finish`方法。

另外，前面说过，`finish`方法中也调用了`promoteAndExecute()`方法，说明 同步/异步 请求 结束后 也会重新调度当前的异步请求。

好了，到这里我们把 调度流程 梳理下：

1. 发起 同步 请求后，`RealCall`使用`Dispatcher`存入`runningSyncCalls`，然后使用`getResponseWithInterceptorChain()`获取结果，最后调用`Dispatcher`的`finish`方法结束请求。

2. 发起 异步 请求后，`RealCall`使用`Dispatcher`存入`readyAsyncCalls`，获得`host`并发数，使用`promoteAndExecute()`方法 在 控制异步并发 的策略基础上，使用 线程池 执行异步请求（并发控制有包括 最大并发数`64`、`host`最大并发数`5`）。异步请求的执行 也是使用`getResponseWithInterceptorChain()`，获得结果后回调出去。最后调用`Dispatcher`的`finish`方法结束请求。

3. `Dispatcher`：调度器，主要是异步请求的并发控制、把异步请求放入线程池执行，实现方法是`promoteAndExecute()`。 `promoteAndExecute()`有两处调用：添加异步请求时、同步/异步请求 结束时。

<br/>

# 请求的执行

<br/>

通过上面分析指导，无论同步还是异步请求，最终的执行都是在`RealCall`的`getResponseWithInterceptorChain()`方法，只不过异步请求 需要先通过`Dispatcher`进行并发控制和线程池处理。那么就来看看`getResponseWithInterceptorChain()`：

```java
Response getResponseWithInterceptorChain() throws IOException {
	List<Interceptor> interceptors = new ArrayList<>();
	interceptors.addAll(client.interceptors());         //使用者配置的 应用拦截器，最先拦截
	interceptors.add(new RetryAndFollowUpInterceptor(client));//重试跟进拦截器
	interceptors.add(new BridgeInterceptor(client.cookieJar()));//桥拦截器
	interceptors.add(new CacheInterceptor(client.internalCache()));//缓存拦截器
	interceptors.add(new ConnectInterceptor(client));   //连接拦截器
	if (!forWebSocket) {
	  interceptors.addAll(client.networkInterceptors()); //使用者配置的网络拦截器
	}
	interceptors.add(new CallServerInterceptor(forWebSocket)); //请求服务拦截器
	//拦截器链
	Interceptor.Chain chain = new RealInterceptorChain(interceptors, transmitter, null, 0,
	    originalRequest, this, client.connectTimeoutMillis(),
	    client.readTimeoutMillis(), client.writeTimeoutMillis());

	boolean calledNoMoreExchanges = false;
	try {
	  Response response = chain.proceed(originalRequest);//链 开始执行
	  if (transmitter.isCanceled()) {
	    closeQuietly(response);
	    throw new IOException("Canceled");
	  }
	  return response;
	} catch (IOException e) {
	  calledNoMoreExchanges = true;
	  throw transmitter.noMoreExchanges(e);
	} finally {
	  if (!calledNoMoreExchanges) {
	    transmitter.noMoreExchanges(null);
	  }
	}
}
```

首先是把

- 应用拦截器（外部配置）`client.interceptors()`
- 重试跟进拦截器`RetryAndFollowUpInterceptor`
- 桥拦截器`BridgeInterceptor`
- 缓存拦截器`CacheInterceptor`
- 连接拦截器`ConnectInterceptor`
- 网络拦截器（外部配置）`client.networkInterceptors()`
- 请求服务拦截器`CallServerInterceptor`

依次 添加到集合`interceptors`中。然后使用`interceptors`、`transmitter`、`originalRequest`等创建了拦截器链`RealInterceptorChain`实例，最后用`proceed`方法获取到请求的结果`Response`。

在上一篇 使用方法中有提到拦截器`Interceptor`，那里配置的拦截器 实际就是 应用拦截器：`client.interceptors()`，是最早被添加到`interceptors`中。那么到底 拦截器是个啥呢？`chain.proceed`是如何获取到结果的呢？不着急，我们先看看`Interceptor`类：

```java
s//拦截器
public interface Interceptor {

  Response intercept(Chain chain) throws IOException;

  //拦截器链
  interface Chain {

    Request request();

    //Chain的核心方法
    Response proceed(Request request) throws IOException;

    //返回请求执行的 连接. 仅网络拦截器可用; 应用拦截器就是null.
    @Nullable Connection connection();

    Call call();

    int connectTimeoutMillis();

    Chain withConnectTimeout(int timeout, TimeUnit unit);

    int readTimeoutMillis();

    Chain withReadTimeout(int timeout, TimeUnit unit);

    int writeTimeoutMillis();

    Chain withWriteTimeout(int timeout, TimeUnit unit);
  }
}
```

`Interceptor`是个接口类，只有一个`intercept`方法，参数是`Chain`对象。再注意到 内部接口类`Chain – 拦截器链`，有个`proceed`方法，参数是`Request`对象，返回值是`Response`，那么这个方法的实现就是请求的处理过程了。`Chain`的唯一实现类就是`RealInterceptorChain`，负责把所有拦截器串联起来，`proceed`方法就是串联的操作。

上述一系列的拦截器都是`Interceptor`的实现类，这里先贴出上一篇中实现的应用拦截器（其他拦截器的实现暂不去跟进）：

```java
new Interceptor() {
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
}
```

在`intercept`方法中我们调用`chain.proceed`方法获取了结果 并在前后打印了一些日志，那这个`Chain`实例是哪来的呢？`intercept`方法啥时候被调用的呢？— — 我们再回头看`getResponseWithInterceptorChain`方法，所有拦截器都被传入`RealInterceptorChain`，可以猜想到，必定是`RealInterceptorChain`的`proceed`方法内部调用了拦截器的`intercept`方法。 那么就来看看吧：

```java
  @Override public Response proceed(Request request) throws IOException {
    return proceed(request, transmitter, exchange);
  }

  public Response proceed(Request request, Transmitter transmitter, @Nullable Exchange exchange)
      throws IOException {
    if (index >= interceptors.size()) throw new AssertionError();

    calls++;

    // If we already have a stream, confirm that the incoming request will use it.
    if (this.exchange != null && !this.exchange.connection().supportsUrl(request.url())) {
      throw new IllegalStateException("network interceptor " + interceptors.get(index - 1)
          + " must retain the same host and port");
    }

    // If we already have a stream, confirm that this is the only call to chain.proceed().
    if (this.exchange != null && calls > 1) {
      throw new IllegalStateException("network interceptor " + interceptors.get(index - 1)
          + " must call proceed() exactly once");
    }

    // Call the next interceptor in the chain.
    RealInterceptorChain next = new RealInterceptorChain(interceptors, transmitter, exchange,
        index + 1, request, call, connectTimeout, readTimeout, writeTimeout);
    Interceptor interceptor = interceptors.get(index);
    Response response = interceptor.intercept(next);

    // Confirm that the next interceptor made its required call to chain.proceed().
    if (exchange != null && index + 1 < interceptors.size() && next.calls != 1) {
      throw new IllegalStateException("network interceptor " + interceptor
          + " must call proceed() exactly once");
    }

    // Confirm that the intercepted response isn't null.
    if (response == null) {
      throw new NullPointerException("interceptor " + interceptor + " returned null");
    }

    if (response.body() == null) {
      throw new IllegalStateException(
          "interceptor " + interceptor + " returned a response with no body");
    }

    return response;
  }
```

在实例化`RealInterceptorChain`时 `index`赋值是`0`，`exchange`是`null`，所以前面三个`if`都没走进去。然后获取了第一个拦截器，也就是我们配置的应用拦截器，调用了它的`interceptor`方法，并返回和校验了结果。这里证实了我们猜想。同时注意到，调用 应用拦截器的`interceptor`方法传入的参数：拦截器链实例`next`，`next`就是把`index + 1`而已，其他参数和当前实例是一样的。也就是说 在我们的应用拦截器中调用的是 `next`的`proceed`方法。

进一步，`next`的`proceed`方法中 同样会获取`interceptors`的`index=1`的拦截器，即`RetryAndFollowUpInterceptor`实例，然后调用其`interceptor`方法，参数是`index+1`即`index=2`的`chain`。跟进`RetryAndFollowUpInterceptor`的代码发现，`interceptor`方法内部也是有调用`chain`的`proceed`方法。这样就会依次传递下去，直到最后一个拦截器`CallServerInterceptor`。

实际上 除了最后一个拦截器`CallServerInterceptor`之外，所有拦截器的`interceptor`方法都调用了 传入 `chain`的`proceed`方法。每个拦截器在`chain`的`proceed`方法 前后 处理了自己负责的工作。例如我们的应用拦截器，在`chain`的`proceed`方法前 打印了`request`信息的日志，`chain`的`proceed`方法获取结果 之后 打印了`response`信息的日志。每个拦截器`interceptor`方法在 调用`chain`的`proceed`方法时 都是为了获取下一个拦截器处理的`response`，然后返回给上一个拦截器。

逻辑总结如下图：

![](/images/java/9_0.png)

这就是 `okhttp`执行流程的核心了，整体流程如下：

![](/images/java/9_1.png)

现在来总结下：

- `拦截器链`：把原始请求 `request` 依次 传入到 每个拦截器。拦截器 处理后 把response 反向 依次 回传。
- `拦截器`：可以对`request`进行处理，然后调用`index+1`的拦截器链`proceed`方法 获取下一个拦截器处理的结果，接着自己也可以处理这个结果，即： 处理`request`、`chain.proceed`、处理`response`。

不知你有没有发现，这一过程 和 公司工作生产流程 很像：

1. 老板接到一笔订单，要求10天内生产100台电脑。

2. 总经理拿到任务后，修改了任务和时间：8天内生产110台，这是基于 生产合格率 以及进行重工、检验、包装、运输的时间上的考量，既要保质保量，也要按时交货。

3. 任务接着到了部门经理，部门经理先去确认了仓库中是否有足够存货，如果有就直接使用存货来交货，这样不存在任何交货风险（质量、时间）；如果没有存货，那么就去要求生产线生产。

4. 生产线按时按量生产完以后，会把生产情况 上报给部门经理，部门经理把结果总结成excel呈现给总经理，总经理则会把整个生产流程结果及各部门的配合情况，总结成PPT报告给老板。

而不同的拦截器，在网络请求这一任务中，就扮演着不同的角色。可能okhttp的作者写拦截器的灵感就来源于生活吧，哈哈。


|拦截器|	作用|
|---|---|
|应用拦截器|	处理原始请求和最终的响应：可以添加自定义header、通用参数、参数加密、网关接入等等。|
|RetryAndFollowUpInterceptor|	处理错误重试和重定向|
|BridgeInterceptor|	应用层和网络层的桥接拦截器，主要工作是为请求添加cookie、添加固定的header，比如Host、Content-Length、Content-Type、User-Agent等等，然后保存响应结果的cookie，如果响应使用gzip压缩过，则还需要进行解压。|
|CacheInterceptor|	缓存拦截器，获取缓存、更新缓存。如果命中缓存则不会发起网络请求。|
|ConnectInterceptor|	连接拦截器，内部会维护一个连接池，负责连接复用、创建连接（三次握手等等）、释放连接以及创建连接上的socket流。|
|网络拦截器|	用户自定义拦截器，通常用于监控网络层的数据传输。|
|CallServerInterceptor	|请求拦截器，在前置准备工作完成后，真正发起网络请求，进行IO读写。|

这里先大概知道每个拦截器的角色任务，下一篇将会详细分析每个拦截器，以及重要知识点–缓存和连接池。

那么，我们对okhttp执行流程的源码分析，到这里也结束了。