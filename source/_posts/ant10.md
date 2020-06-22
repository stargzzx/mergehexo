---
title: ant | RestApi
date: 2020-06-22 15:37:39
categories:
- [项目经历,大型,ant]
tags:
- ant
password: antant10
abstract: ant 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
交易所对象中的 RestApi。

<!-- more -->

<br/>

# XXXRestApi

<br/>

```python
class XXXRestApi(RestClient):

    def __init__(self, gateway: BaseGateway):
        """"""
        super().__init__(gateway.gateway_name)
        self.gateway = gateway
        self.gateway_name = gateway.gateway_name
        self.exchange_data_manager = gateway.exchange_data_manager

    def connect(self, setting: dict):
        """
        Initialize connection to REST server.
        """
        self.init(REST_HOST, setting["proxy_host"], setting["proxy_port"])
        self.start(setting["session_number"])
        self.gateway.log("REST API启动成功")

        self.query_contract()

    def query_contract(self):
        """"""
        self.add_request(
            method="GET",
            path=f"***",
            callback=self.on_query_contract,
        )

    def on_query_contract(self, data, request):  # type: (dict, Request)->None
        """"""
        pass
```

可以看出该对象继承了 `RestClient` ,其有一个比较重要的方法是

- connect()

这个主要是通过 `MainEngine` 来调用。主要目的是为了检测是否可以连接成功，并初始化和开启进程池。

- init()
	- 一些参数的初始化
- start()
	- 开启进程池，下面详细说明

并且在这里我们先看一下，里面是怎么调用请求的。

```python
self.add_request(
            method="GET",
            path=f"***",
            callback=self.on_query_contract,
        )
```

对于 `XXXRestApi` 来说，显示调用的函数是

- query_contract()

这个函数的目的是为了得到交易所返回的交易对，并和数据库存储的交易对进行比对，看是否正确。

只有正确后，系统才有可能进行下一步的动作。

<br/>

# RestClient

<br/>

这个是核心对象。

在看这个对象之前，我们先看一下，我们自己设置了一个 `request` 对象

## request

```python
class Request(object):
    """
    Request object for status check.
    """

    def __str__(self):
        if self.response is None:
            status_code = "terminated"
        else:
            status_code = self.response.status_code

        return (
            "request : {} {} {} because {}: \n"
            "headers: {}\n"
            "params: {}\n"
            "data: {}\n"
            "response:"
            "{}\n".format(
                self.method,
                self.path,
                self.status.name,
                status_code,
                self.headers,
                self.params,
                self.data,
                "" if self.response is None else self.response.text,
            )
        )

    def __init__(
            self,
            method: str,
            path: str,
            params: dict,
            data: Union[dict, str, bytes],
            headers: dict,
            callback: Callable = None,
            on_failed: Callable = None,
            on_error: Callable = None,
            extra: Any = None,
            timeout: int = 5,
            retry_times: int = 3,
    ):
        """"""
        self.method = method
        self.path = path
        self.callback = callback
        self.params = params
        self.data = data
        self.headers = headers

        self.on_failed = on_failed
        self.on_error = on_error
        self.extra = extra

        self.response = None
        self.status = RequestStatus.ready
        self.timeout = timeout
        self.retry_times = retry_times  # 重试次数，默认最多请求三次
```

先记住这个对象，我们把其中一些比较重要的参数说一下

- method
	- 请求方法
- path
	- 请求 url
- callback
	- 回调函数
- params
	- get 方法参数
- data
	- post 方法参数

## 初始化

`XXXRestApi` 的 `connect` 调用了 `RestClient` 的两个方法

- init()
- start()

### init()

这个方法只是初始化一些变量，如代理、原始路径等，没有什么值得说的

### start()

这个方法是创建了一个进程池（一般是3个进程），并且，开启了进程方法 `_run` ，进程的通信用的是队列。

## 请求

我们回过头来看一下 `XXXRestApi` 的请求。

```python
def query_contract(self):
    """"""
    self.add_request(
        method="GET",
        path="***",
        callback=self.on_query_contract,
    )
```

当调用 `add_request` 方法的时候，也就是调用了 `RestClient` 的 `add_request` 方法

```python
def add_request(
        self,
        method: str,
        path: str,
        callback: Callable,
        params: dict = None,
        data: Union[dict, str, bytes] = None,
        headers: dict = None,
        on_failed: Callable = None,
        on_error: Callable = None,
        extra: Any = None,
        timeout: int = 10,
        retry_times: int = 3,
):
    if self.is_data_producer and not self.monitor_engine.get_gateway_status(self.gateway_name):
        return
    request = Request(
        method,
        path,
        params,
        data,
        headers,
        callback,
        on_failed,
        on_error,
        extra,
        timeout,
        retry_times,
    )

    if self.is_data_producer:
        if self._queue.qsize() < self.queue_pool_size:
            self._queue.put(request)
    else:
        self._queue.put(request)
```

可以看到，首先，先把参数拟合成自创建的 `request` 对象。然后把该对象放到 `_queue` 中「`_queue` 是队列，用于进程间的消息传递」。

## 循环

在上面我们说到 `_run` 方法，这个就是为了从 `_queue` 中获取请求，然后进行返回数据传递。

### `_run()`

```python
def _run(self):
    try:
        session = self._create_session()
        while self._active:
            try:
                request = self._queue.get(timeout=1)
                try:
                    if self.is_data_producer:
                        if self.monitor_engine.get_gateway_status(self.gateway_name):
                            self._process_request(request, session)
                        else:
                            sleep(1)
                            self.log(f"request is not connect {request.path}")
                    else:
                        self._process_request(request, session)
                finally:
                    self._queue.task_done()

            except Empty:
                pass
    except Exception:
        et, ev, tb = sys.exc_info()
        sleep(random.randint(1, 2))
        self.on_error(et, ev, tb, None)
```

从队列中取出 `request` 然后，传递到 `_process_request` 中，其中

	session = self._create_session()

调用的是

	requests.session()

接下来，我们看 `_process_request` 方法。

### `_process_request()`

```python
def _process_request(
        self, request: Request, session: requests.Session
):
    """
    Sending request to server and get result.
    """
    try:
        request = self.sign(request)
        url = self.make_full_url(request.path)
        status_code = 0

        while request.retry_times > 0:
            try:
                response = session.request(
                    request.method,
                    url,
                    headers=request.headers,
                    params=request.params,
                    data=request.data,
                    proxies=self.proxies,
                    timeout=request.timeout
                )

                request.response = response
                status_code = response.status_code
                if status_code // 100 != 2:
                    request.retry_times -= 1
                else:
                    break
            except Exception as e:
                request.retry_times -= 1
                if request.retry_times < 1:
                    raise e

        if status_code // 100 == 2:  # 2xx codes are all successful
            if status_code == 204:
                json_body = None
            else:
                json_body = response.json()

            request.callback(json_body, request)
            request.status = RequestStatus.success
        else:
            request.status = RequestStatus.failed

            if request.on_failed:
                request.on_failed(status_code, request)
            else:
                self.on_failed(status_code, request)
    except Exception:
        sleep(random.randint(1, 2))
        request.status = RequestStatus.error
        t, v, tb = sys.exc_info()
        if request.on_error:
            request.on_error(t, v, tb, request)
        else:
            self.on_error(t, v, tb, request)
```

在这里面，我们可以看到

我们拿到了 `request` 之后，请求 `request` 的方法，然后把返回的数据和 `request` 返回到 `callback` 上。

至此，一个完整的轮回就讲解完毕。

下面我来贴一下 `rest_client.py` 的总代码。

<br/>

# rest_client.py 总代码

<br/>

```python
import sys
import traceback
from copy import copy
from datetime import datetime
from enum import Enum
from multiprocessing.dummy import Pool
from queue import Empty, Queue
from typing import Any, Callable, Optional, Union
from ant.monitor.engine import MonitorEngine
import requests
from threading import currentThread, Thread
from time import sleep
import random


class RequestStatus(Enum):
    ready = 0  # Request created
    success = 1  # Request successful (status code 2xx)
    failed = 2  # Request failed (status code not 2xx)
    error = 3  # Exception raised


class Request(object):
    """
    Request object for status check.
    """

    def __str__(self):
        if self.response is None:
            status_code = "terminated"
        else:
            status_code = self.response.status_code

        return (
            "request : {} {} {} because {}: \n"
            "headers: {}\n"
            "params: {}\n"
            "data: {}\n"
            "response:"
            "{}\n".format(
                self.method,
                self.path,
                self.status.name,
                status_code,
                self.headers,
                self.params,
                self.data,
                "" if self.response is None else self.response.text,
            )
        )

    def __init__(
            self,
            method: str,
            path: str,
            params: dict,
            data: Union[dict, str, bytes],
            headers: dict,
            callback: Callable = None,
            on_failed: Callable = None,
            on_error: Callable = None,
            extra: Any = None,
            timeout: int = 5,
            retry_times: int = 3,
    ):
        """"""
        self.method = method
        self.path = path
        self.callback = callback
        self.params = params
        self.data = data
        self.headers = headers

        self.on_failed = on_failed
        self.on_error = on_error
        self.extra = extra

        self.response = None
        self.status = RequestStatus.ready
        self.timeout = timeout
        self.retry_times = retry_times  # 重试次数，默认最多请求三次


class RestClient(object):
    """
    HTTP Client designed for all sorts of trading RESTFul API.

    * Reimplement sign function to add signature function.
    * Reimplement on_failed function to handle Non-2xx responses.
    * Use on_failed parameter in add_request function for individual Non-2xx response handling.
    * Reimplement on_error function to handle exception msg.
    """

    def __init__(self, gateway_name, queue_pool_size=10000):
        """
        """
        self.url_base = ''  # type: str
        self._active = False
        self.gateway_name = gateway_name
        self._queue = Queue(maxsize=100000)
        self._pool = None  # type: Pool
        self.proxies = None
        self.log = None
        self.monitor_engine = None
        self.queue_pool_size = queue_pool_size
        self.is_data_producer = False

    def init(self, url_base: str, proxy_host: str = "", proxy_port: str = '0', log=None):
        """
        Init rest client with url_base which is the API root address.
        e.g. 'https://www.bitmex.com/api/v1/'
        """
        self.url_base = url_base
        if proxy_host and proxy_port:
            if proxy_host and proxy_port:
                proxy = f"http://{proxy_host}:{proxy_port}"
                self.proxies = {"http": proxy, "https": proxy}
        if "is_data_producer" in self.setting.keys():
            self.is_data_producer = self.setting["is_data_producer"]
        self.log = log
        self.monitor_engine = MonitorEngine.instance(log=log)

    def set_url(self, url):
        self.url_base = url

    def _create_session(self):
        """"""
        return requests.session()

    def start(self, n: int = 3):
        """
        Start rest client with session count n.
        """
        if self._active:
            return

        self._active = True
        self._pool = Pool(n)
        self._pool.apply_async(self._run)

    def stop(self):
        """
        Stop rest client immediately.
        """
        self._active = False

    def join(self):
        """
        Wait till all requests are processed.
        """
        self._queue.join()

    def add_request(
            self,
            method: str,
            path: str,
            callback: Callable,
            params: dict = None,
            data: Union[dict, str, bytes] = None,
            headers: dict = None,
            on_failed: Callable = None,
            on_error: Callable = None,
            extra: Any = None,
            timeout: int = 10,
            retry_times: int = 3,
    ):
        """
        Add a new request.
        :param timeout: Maximum waiting timeout
        :param retry_times: Retry times if request failed. default is 3
        :param method: GET, POST, PUT, DELETE, QUERY
        :param path:
        :param callback: callback function if 2xx status, type: (dict, Request)
        :param params: dict for query string
        :param data: Http body. If it is a dict, it will be converted to form-data. Otherwise, it will be converted to bytes.
        :param headers: dict for headers
        :param on_failed: callback function if Non-2xx status, type, type: (code, dict, Request)
        :param on_error: callback function when catching Python exception, type: (etype, evalue, tb, Request)
        :param extra: Any extra data which can be used when handling callback
        :return: Request
        """

        if self.is_data_producer and not self.monitor_engine.get_gateway_status(self.gateway_name):
            return
        request = Request(
            method,
            path,
            params,
            data,
            headers,
            callback,
            on_failed,
            on_error,
            extra,
            timeout,
            retry_times,
        )

        if self.is_data_producer:
            if self._queue.qsize() < self.queue_pool_size:
                self._queue.put(request)
        else:
            self._queue.put(request)

    def _run(self):
        try:
            session = self._create_session()
            while self._active:
                try:
                    request = self._queue.get(timeout=1)
                    try:
                        if self.is_data_producer:
                            if self.monitor_engine.get_gateway_status(self.gateway_name):
                                self._process_request(request, session)
                            else:
                                sleep(1)
                                self.log(f"request is not connect {request.path}")
                        else:
                            self._process_request(request, session)
                    finally:
                        self._queue.task_done()

                except Empty:
                    pass
        except Exception:
            et, ev, tb = sys.exc_info()
            sleep(random.randint(1, 2))
            self.on_error(et, ev, tb, None)

    def sign(self, request: Request):
        """
        This function is called before sending any request out.
        Please implement signature method here.
        @:return (request)
        """
        return request

    def on_failed(self, status_code: int, request: Request):
        """
        Default on_failed handler for Non-2xx response.
        """
        self.log(f"{str(request)} {request.path}")

    def on_error(
            self,
            exception_type: type,
            exception_value: Exception,
            tb,
            request: Optional[Request],
    ):
        """
        Default on_error handler for Python exception.
        """
        self.log(self.exception_detail(exception_type, exception_value, tb, request))

    def exception_detail(
            self,
            exception_type: type,
            exception_value: Exception,
            tb,
            request: Optional[Request],
    ):
        text = "[{}]: Unhandled RestClient Error:{}\n".format(
            datetime.now().isoformat(), exception_type
        )
        text += "request:{}\n".format(request)
        text += "Exception trace: \n"
        text += "".join(
            traceback.format_exception(exception_type, exception_value, tb)
        ) + "\n"
        if request is not None:
            text += request.path
        return text

    def _process_request(
            self, request: Request, session: requests.Session
    ):
        """
        Sending request to server and get result.
        """
        try:
            request = self.sign(request)
            url = self.make_full_url(request.path)
            status_code = 0

            while request.retry_times > 0:
                try:
                    response = session.request(
                        request.method,
                        url,
                        headers=request.headers,
                        params=request.params,
                        data=request.data,
                        proxies=self.proxies,
                        timeout=request.timeout
                    )

                    request.response = response
                    status_code = response.status_code
                    if status_code // 100 != 2:
                        request.retry_times -= 1
                    else:
                        break
                except Exception as e:
                    request.retry_times -= 1
                    if request.retry_times < 1:
                        raise e

            if status_code // 100 == 2:  # 2xx codes are all successful
                if status_code == 204:
                    json_body = None
                else:
                    json_body = response.json()

                request.callback(json_body, request)
                request.status = RequestStatus.success
            else:
                request.status = RequestStatus.failed

                if request.on_failed:
                    request.on_failed(status_code, request)
                else:
                    self.on_failed(status_code, request)
        except Exception:
            sleep(random.randint(1, 2))
            request.status = RequestStatus.error
            t, v, tb = sys.exc_info()
            if request.on_error:
                request.on_error(t, v, tb, request)
            else:
                self.on_error(t, v, tb, request)

    def make_full_url(self, path: str):
        """
        Make relative api path into full url.
        eg: make_full_url('/get') == 'http://xxxxx/get'
        """
        url = self.url_base + path
        return url

    def request(
            self,
            method: str,
            path: str,
            params: dict = None,
            data: dict = None,
            headers: dict = None,
    ):
        """
        Add a new request.
        :param method: GET, POST, PUT, DELETE, QUERY
        :param path:
        :param params: dict for query string
        :param data: dict for body
        :param headers: dict for headers
        :return: requests.Response
        """
        request = Request(
            method,
            path,
            params,
            data,
            headers
        )
        request = self.sign(request)

        url = self.make_full_url(request.path)

        response = requests.request(
            request.method,
            url,
            headers=request.headers,
            params=request.params,
            data=request.data,
            proxies=self.proxies,
        )
        return response
```