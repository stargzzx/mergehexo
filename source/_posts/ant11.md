---
title: ant | Websocket
date: 2020-06-22 17:58:45
categories:
- [项目经历,大型,ant]
tags:
- ant
password: antant11
abstract: ant 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
ant 项目的核心讲解 —— websocket。

<!-- more -->

<br/>

# XXXWebsocketApi

<br/>

```python
class XXXWebsocketApi(WebsocketClient):
    """"""

    def __init__(self, gateway):
        """"""
        super().__init__(gateway.log)

        self.gateway = gateway
        self.gateway_name = gateway.gateway_name
        self.exchange_data_manager = gateway.exchange_data_manager

    def connect(self, setting: dict):
        """"""
        self.setting = setting
        self.init(WEBSOCKET_HOST, Exchange.XXX, setting["proxy_host"], setting["proxy_port"], ping_interval=5,
                  is_data_producer=setting["is_data_producer"])
        self.start()

    def on_connected(self):
        """"""
        web_socket_connection = WebSocketConnection(
            web_socket_type=WebsocketType.SPOT_MARKET,
            is_connect=True,
            exchange=Exchange.XXX)
        self.gateway.on_web_socket_connection(web_socket_connection)
        self.gateway.log("UpBit 行情Websocket API连接刷新")

    def subscribe(self, req: SubscribeRequest):
        """"""
        ...
        self.send_packet(_req)

    def on_packet(self, packet):
        """"""
        pass
```

我们看一看到，`XXXWebsocketApi` 继承于 `WebsocketClient`。这里面有几个重要的方法是

- connect()
- on_connected()
- subscribe()
- on_packet()

## connect()

由 `MainEngine` 主动调用，目的是为了查看 websocket 是否连接成功。其内部的作用是初始化对象和开启相关的线程。

## on_connect()

这个是由 websocket 主动返回执行。

## subscribe()

这个是通过 `KafkaEngine` 来调用的，当确定 rest 和 websocket 都能够 connect 的时候，就会调用该订阅函数。

## on_packet()

当有数据推过来的时候，就会解析并传到该函数上。

<br/>

# WebsocketClient

<br/>

接下来，我们再来解析父类对象， `WebsocketClient`。

首先是 `XXXWebsocketApi` 的 connect 调用的两个方法

- init()
- start()

init() 这个是初始化变量，不需要说，重点说一下， start().

## start()

```python
def start(self):
    """
    Start the client and on_connected function is called after webscoket
    is connected succesfully.

    Please don't send packet untill on_connected fucntion is called.
    """

    self._active = True
    self._worker_thread = Thread(target=self._run)
    self._worker_thread.start()

    self._ping_thread = Thread(target=self._run_ping)
    self._ping_thread.start()
```

该 start() 函数，主要是开启了两个线程。

- `_run`
- `_run_ping`

其中 `_run_ping` 是为了和交易所通信心跳，没什么好说的，重点说一下 `_run`。

## `_run()`

```python
def _run(self):
    """
    Keep running till stop is called.
    """
    try:
        while self._active:
            try:
                self._ensure_connection()
                ws = self._ws
                if ws:
                    text = ws.recv()

                    # ws object is closed when recv function is blocking
                    if not text:
                        self._disconnect()
                        continue

                    self._record_last_received_text(text)

                    try:
                        data = self.unpack_data(text)
                    except ValueError as e:
                        self.log(f"exchange={self.exchange.value} websocket unable to parse data:{text}", ERROR)
                        raise e

                    self.on_packet(data)
            except (websocket.WebSocketConnectionClosedException,
                    websocket.WebSocketBadStatusException,
                    websocket.WebSocketProxyException,
                    socket.error):
                self._disconnect()
            except:
                et, ev, tb = sys.exc_info()
                self.on_error(et, ev, tb)
                self._disconnect()
    except:
        et, ev, tb = sys.exc_info()
        self.on_error(et, ev, tb)
    self._disconnect()
```

首先，websocket 的 `_run` 会先执行

	self._ensure_connection()

### `_ensure_connection()`

```python
def _ensure_connection(self):
    """"""
    triggered = False
    with self._ws_lock:
        if self._ws is None and (self.disconnect or self.first_connect):
            self.first_connect = False
            if self.is_data_producer:
                sleep(random.randint(1, 5))
            self.log(f"exchange:{self.exchange.value} _ensure_connection")
            self._ws = self._create_connection(
                self.host,
                sslopt={"cert_reqs": ssl.CERT_NONE},
                http_proxy_host=self.proxy_host,
                http_proxy_port=self.proxy_port,
                header=self.header
            )
            triggered = True
            self.disconnect = False
            self.on_reconnected()
    if triggered:
        self.on_connected()
```

该函数的职能如下：

当断开连接或者是第一次连接的时候，会调用 `on_reconnected()` 方法。

### on_reconnected()

```python
def on_reconnected(self):
    """
    Callback when webscoket reconnected
    :return:
    """
    self.gateway.on_web_socket_reconnection(self.exchange)
```

然后会给 `EventEngine` 传递一个事件，最终该事件会调用

	self.main_engine.subscribe(req, contract.gateway_name)

这个方法，然后调用

`XXXWebsocketApi` 的 `subscribe()` 方法，然后完成订阅，这个方法，我们之后再说，继续回到 `_ensure_connection`。

第一个情况说完了，第二个情况是，一切都很顺利，所以，调用了 `on_connected()` 方法。

### on_connected()

```python
@staticmethod
def on_connected():
    """
    Callback when websocket is connected successfully.
    """
    pass
```

所以，调用了 `XXXWebsocketApi` 中的 `on_connected` 方法。

该方法，会向 `EventEngine` 中注册一个事件，然后证明 websocket 已经连接成功。

接着看 `_run` 方法，确定连接后，我们就进入了

	text = ws.recv()

这个是交易所返回数据，然后 ws 接收。为什么会返回数据，是因为 `subscribe()` 函数，这个我们后面再说。

这个方法一共两个情况，第一个情况是有数据返回。

那么其会调用

```python
self._record_last_received_text(text)

try:
    data = self.unpack_data(text)
except ValueError as e:
    self.log(f"exchange={self.exchange.value} websocket unable to parse data:{text}", ERROR)
    raise e

self.on_packet(data)
```

这个情况会把返回来的数据进行解析，然后，传递给 `on_packet` 函数，由于在 `WebsocketClient` 中的

```python
@staticmethod
def on_packet(packet: dict):
    """
    Callback when receiving data from server.
    """
    pass
```

所以，也就传递给了 `XXXWebsocketapi` 的 `on_packet()` 中。

如果，没有数据返回，则考虑是不是断开了连接。

所以，会触发

	self._disconnect()

### `_disconnect()`

```python
def _disconnect(self):
    """
    """
    triggered = False
    with self._ws_lock:
        if self._ws:
            if self.is_data_producer:
                if time() - self.disconnect_map.get(self.exchange, 0) < 60:
                    self.disconnect = False
                    return
            self.disconnect_map[self.exchange] = time()
            ws: websocket.WebSocket = self._ws
            self._ws = None
            triggered = True
            self.disconnect = True

    if triggered:
        ws.close()
        self.on_disconnected()
```

这个也会分两种情况，一个是判断是不是时间太短了，导致没有数据传过来，但是，如果真的没有数据传过来

首先会

    self._ws = None
    self.disconnect = True

因为这两个数值变化了，所以， `_run` 会进行重订阅，调用 `on_reconnected()`。

然后关掉 websocket 连接，然后，调用

	self.on_disconnected()

### on_disconnected()

```python
def on_disconnected(self):
    """
    Callback when websocket connection is lost.
    """
    self.log(f"{self.exchange} websocket 断开连接")
    self.gateway.on_web_socket_disconnection(self.exchange)
```

事实上，只有 `XXXWebsocketApi` 的

- connect()
- on_connected()

都执行完毕后，才会执行 `subscribe()`。

## subscribe

```python
def subscribe(self, req: SubscribeRequest):
    """"""
    ...
    self.send_packet(_req)

```

这个是 `MainEngine` 调用的，然后其会向建立连接的 ws 来进行内容订阅。

之后传递到的内容会传给 ws ，也就是通过 `_run` 来接收。

也就是上面的 `_run` 函数中的

    text = ws.recv()

至此，大概的内容算是讲完了。

在这里，贴一下 `websocketclient` 的全部代码。

<br/>

# websocket_client.py

<br/>

```python
import json
import ssl
import sys
import traceback
import socket
import random
from datetime import datetime
from threading import Lock, Thread
from time import sleep, time
from logging import ERROR
import websocket
from ant.trader.constant import (
    Exchange,
)


class WebsocketClient(object):
    """
    Websocket API

    After creating the client object, use start() to run worker and ping threads.
    The worker thread connects websocket automatically.

    Use stop to stop threads and disconnect websocket before destroying the client
    object (especially when exiting the programme).

    Default serialization format is json.

    Callbacks to overrides:
    * unpack_data
    * on_connected
    * on_disconnected
    * on_packet
    * on_error

    After start() is called, the ping thread will ping server every 60 seconds.

    If you want to send anything other than JSON, override send_packet.
    """

    def __init__(self, log):
        """Constructor"""
        self.host = None

        self._ws_lock = Lock()
        self._ws = None

        self._worker_thread = None
        self._ping_thread = None
        self._active = False
        self.disconnect = False
        self.first_connect = True
        self.disconnect_map = {}
        self.proxy_host = None
        self.proxy_port = None
        self.ping_interval = 20  # seconds
        self.exchange = None
        self.header = {}

        # For debugging
        self._last_sent_text = None
        self._last_received_text = None
        self.log = log
        self.is_data_producer = False
        self.no_ping_exchange = [Exchange.HUOBI_FUTURES, Exchange.HUOBI, Exchange.HUOBI_SWAP]
        self.ping_exchange = [Exchange.BYBIT_SWAP, Exchange.BYBIT_USWAP, Exchange.FTX_SWAP, Exchange.BITMAX,
                              Exchange.FTX_FUTURES]

    def init(self, host: str, exchange: Exchange, proxy_host: str = "", proxy_port: int = 0,
             ping_interval: int = 60, is_data_producer=False, header: dict = None):
        """
        """
        self.host = host
        self.ping_interval = ping_interval  # seconds

        if header:
            self.header = header

        if proxy_host and proxy_port:
            self.proxy_host = proxy_host
            self.proxy_port = proxy_port

        self.exchange = exchange
        self.ping_interval = ping_interval
        self.is_data_producer = is_data_producer

    def start(self):
        """
        Start the client and on_connected function is called after webscoket
        is connected succesfully.

        Please don't send packet untill on_connected fucntion is called.
        """

        self._active = True
        self._worker_thread = Thread(target=self._run)
        self._worker_thread.start()

        self._ping_thread = Thread(target=self._run_ping)
        self._ping_thread.start()

    def stop(self):
        """
        Stop the client.
        """
        self._active = False
        self._disconnect()

    def join(self):
        """
        Wait till all threads finish.

        This function cannot be called from worker thread or callback function.
        """
        self._ping_thread.join()
        self._worker_thread.join()

    def send_packet(self, packet: object):
        """
        Send a packet (dict data) to server

        override this if you want to send non-json packet
        """
        if isinstance(packet, str):
            text = packet
        else:
            text = json.dumps(packet)

        self._record_last_sent_text(text)
        return self._send_text(text)

    def _send_text(self, text: str):
        """
        Send a text string to server.
        """
        ws = self._ws
        try:
            if ws:
                ws.send(text, opcode=websocket.ABNF.OPCODE_TEXT)
        except (websocket.WebSocketConnectionClosedException,
                websocket.WebSocketBadStatusException,
                socket.error):
            self.log(f"exchange={self.exchange.value} ws = {self._ws} text ={text} traceback={traceback.format_exc()} ",
                     ERROR)
            self._disconnect()

    def _send_binary(self, data: bytes):
        """
        Send bytes data to server.
        """
        ws = self._ws
        if ws:
            ws._send_binary(data)

    def _create_connection(self, *args, **kwargs):
        """"""
        return websocket.create_connection(*args, **kwargs)

    def _ensure_connection(self):
        """"""
        triggered = False
        with self._ws_lock:
            if self._ws is None and (self.disconnect or self.first_connect):
                self.first_connect = False
                if self.is_data_producer:
                    sleep(random.randint(1, 5))
                self.log(f"exchange:{self.exchange.value} _ensure_connection")
                self._ws = self._create_connection(
                    self.host,
                    sslopt={"cert_reqs": ssl.CERT_NONE},
                    http_proxy_host=self.proxy_host,
                    http_proxy_port=self.proxy_port,
                    header=self.header
                )
                triggered = True
                self.disconnect = False
                self.on_reconnected()
        if triggered:
            self.on_connected()

    def _disconnect(self):
        """
        """
        triggered = False
        with self._ws_lock:
            if self._ws:
                if self.is_data_producer:
                    if time() - self.disconnect_map.get(self.exchange, 0) < 60:
                        self.disconnect = False
                        return
                self.disconnect_map[self.exchange] = time()
                ws: websocket.WebSocket = self._ws
                self._ws = None
                triggered = True
                self.disconnect = True

        if triggered:
            ws.close()
            self.on_disconnected()

    def _run(self):
        """
        Keep running till stop is called.
        """
        try:
            while self._active:
                try:
                    self._ensure_connection()
                    ws = self._ws
                    if ws:
                        text = ws.recv()

                        # ws object is closed when recv function is blocking
                        if not text:
                            self._disconnect()
                            continue

                        self._record_last_received_text(text)

                        try:
                            data = self.unpack_data(text)
                        except ValueError as e:
                            self.log(f"exchange={self.exchange.value} websocket unable to parse data:{text}", ERROR)
                            raise e

                        self.on_packet(data)
                except (websocket.WebSocketConnectionClosedException,
                        websocket.WebSocketBadStatusException,
                        websocket.WebSocketProxyException,
                        socket.error):
                    self._disconnect()
                except:
                    et, ev, tb = sys.exc_info()
                    self.on_error(et, ev, tb)
                    self._disconnect()
        except:
            et, ev, tb = sys.exc_info()
            self.on_error(et, ev, tb)
        self._disconnect()

    @staticmethod
    def unpack_data(data: str):
        """
        Default serialization format is json.

        override this method if you want to use other serialization format.
        """
        return json.loads(data)

    def _run_ping(self):
        """"""
        while self._active:
            try:
                self._ping()
            except:  # noqa
                et, ev, tb = sys.exc_info()
                self.on_error(et, ev, tb)

                # self._run() will reconnect websocket
                sleep(1)

            for i in range(self.ping_interval):
                if not self._active:
                    break
                sleep(1)

    def _ping(self):
        """"""
        if self.disconnect:
            return
        try:
            ws = self._ws
            if ws:
                if self.exchange in [Exchange.FCOIN, Exchange.FMEX]:
                    text = {"cmd": "ping", "args": [int(time() * 1000)], "id": str(random.randint(0, 99999))}
                    ws.send(json.dumps(text))
                elif self.exchange == Exchange.GATE:
                    text = {"id": random.randint(0, 99999), "method": "server.ping", "params": []}
                    ws.send(json.dumps(text))
                elif self.exchange == Exchange.KRAKEN:
                    text = {"event": "ping"}
                    ws.send(json.dumps(text))
                elif self.exchange == Exchange.LIQUID:
                    text = {'event': 'pusher:ping', "data": {}}
                    ws.send(json.dumps(text))
                elif self.exchange == Exchange.KUCOIN:
                    text = {
                        "id": datetime.now().timestamp() * 1000,
                        "type": "ping"
                    }
                    ws.send(json.dumps(text))
                elif self.exchange == Exchange.BITBANK:
                    ws.send("""42["message":"ping"]""")
                elif self.exchange == Exchange.GATE:
                    ws.send('{"id":2020000, "method":"server.ping", "params":[]}')
                elif self.exchange == Exchange.GATE_SWAP:
                    ws.send('{"time" : 2020000, "channel" : "futures.ping"}')
                elif self.exchange == Exchange.BITFINEX or self.exchange == Exchange.BITFINEX_SWAP:
                    ws.send('{"event": "ping","cid": 1234}')
                elif self.exchange in self.ping_exchange:
                    text = {"op": "ping"}
                    ws.send(json.dumps(text))
                elif self.exchange not in self.no_ping_exchange:
                    ws.send("ping", websocket.ABNF.OPCODE_PING)
        except Exception as e:
            self.log(f"ping exchange={self.exchange.value} exception={e}", ERROR)

    @staticmethod
    def on_connected():
        """
        Callback when websocket is connected successfully.
        """
        pass

    def on_disconnected(self):
        """
        Callback when websocket connection is lost.
        """
        self.log(f"{self.exchange} websocket 断开连接")
        self.gateway.on_web_socket_disconnection(self.exchange)

    def on_reconnected(self):
        """
        Callback when webscoket reconnected
        :return:
        """
        self.gateway.on_web_socket_reconnection(self.exchange)

    @staticmethod
    def on_packet(packet: dict):
        """
        Callback when receiving data from server.
        """
        pass

    def on_error(self, exception_type: type, exception_value: Exception, tb):
        """
        Callback when exception raised.
        """
        self.log(self.exception_detail(exception_type, exception_value, tb))
        return sys.excepthook(exception_type, exception_value, tb)

    def exception_detail(
            self, exception_type: type, exception_value: Exception, tb
    ):
        """
        Print detailed exception information.
        """
        text = "exchange {} [{}]: Unhandled WebSocket Error:{}\n".format(
            self.exchange.value, datetime.now().isoformat(), exception_type
        )
        text += "LastSentText:\n{}\n".format(self._last_sent_text)
        text += "LastReceivedText:\n{}\n".format(self._last_received_text)
        text += "Exception trace: \n"
        text += "".join(
            traceback.format_exception(exception_type, exception_value, tb)
        )
        return text

    def _record_last_sent_text(self, text: str):
        """
        Record last sent text for debug purpose.
        """
        self._last_sent_text = text[:1000]

    def _record_last_received_text(self, text: str):
        """
        Record last received text for debug purpose.
        """
        self._last_received_text = text[:1000]
```