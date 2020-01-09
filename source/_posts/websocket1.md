---
title: python与websocket以及公司的再次封装
date: 2019-11-10 22:25:09
categories:
- websocket
tags:
- websocket
- network
---
这个将要介绍 python 与 websocket 以及，我公司封装的 websocket 的用法。

<!-- more -->

# 参考资料
[websocket-client](https://github.com/websocket-client/websocket-client)

公司封装的 websocket
{% codeblock %}
import json
import ssl
import sys
import traceback
import socket
import random
from datetime import datetime
from threading import Lock, Thread
from time import sleep, time

import websocket
from constant import Exchange


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

    def __init__(self):
        """Constructor"""
        self.host = None

        self._ws_lock = Lock()
        self._ws = None

        self._worker_thread = None
        self._ping_thread = None
        self._active = False
        self.disconnect = False
        self.proxy_host = None
        self.proxy_port = None
        self.ping_interval = 60  # seconds
        self.exchange = None
        self.header = {}

        # For debugging
        self._last_sent_text = None
        self._last_received_text = None

    def init(self, host: str, proxy_host: str = "", proxy_port: int = 0, ping_interval: int = 60, header: dict = None,
             exchange: Exchange = None):
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

    def send_packet(self, packet: dict):
        """
        Send a packet (dict data) to server

        override this if you want to send non-json packet
        """
        text = json.dumps(packet)
        self._record_last_sent_text(text)
        return self._send_text(text)

    def _send_text(self, text: str):
        """
        Send a text string to server.
        """
        ws = self._ws
        if ws:
            ws.send(text, opcode=websocket.ABNF.OPCODE_TEXT)

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
            if self._ws is None:
                self._ws = self._create_connection(
                    self.host,
                    sslopt={"cert_reqs": ssl.CERT_NONE},
                    http_proxy_host=self.proxy_host,
                    http_proxy_port=self.proxy_port,
                    header=self.header
                )
                triggered = True

        if triggered:
            if self.disconnect:
                self.disconnect = False
                self.on_reconnected()
            self.on_connected()

    def _disconnect(self):
        """
        """
        triggered = False
        with self._ws_lock:
            if self._ws:
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
                            print("websocket unable to parse data: " + text)
                            raise e

                        self.on_packet(data)
                # ws is closed before recv function is called
                # For socket.error, see Issue #1608
                except (websocket.WebSocketConnectionClosedException, socket.error):
                    self._disconnect()

                # other internal exception raised in on_packet
                except:  # noqa
                    et, ev, tb = sys.exc_info()
                    self.on_error(et, ev, tb)
                    self._disconnect()
        except:  # noqa
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
        ws = self._ws
        if ws:
            if self.exchange is not None:
                if self.exchange.value in [Exchange.FCOIN.value, Exchange.FMEX.value]:
                    text = {"cmd": "ping", "args": [int(time() * 1000)], "id": str(random.randint(0, 99999))}
                    ws.send(json.dumps(text))
                elif self.exchange.value == Exchange.GATE.value:
                    text = {"id": random.randint(0, 99999), "method": "server.ping", "params": []}
                    ws.send(json.dumps(text))
            else:
                ws.send("ping", websocket.ABNF.OPCODE_PING)

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
        self.gateway.on_web_socket_disconnection()

    def on_reconnected(self):
        """
        Callback when webscoket reconnected
        :return:
        """
        self.gateway.on_web_socket_reconnection()

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
        sys.stderr.write(
            self.exception_detail(exception_type, exception_value, tb)
        )
        return sys.excepthook(exception_type, exception_value, tb)

    def exception_detail(
            self, exception_type: type, exception_value: Exception, tb
    ):
        """
        Print detailed exception information.
        """
        text = "[{}]: Unhandled WebSocket Error:{}\n".format(
            datetime.now().isoformat(), exception_type
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
{% endcodeblock %}

{% codeblock %}
from websocket_client import WebsocketClient


class Bitmex(WebsocketClient):

    def __init__(self):
        super().__init__()
        self.test_host = "wss://www.bitmex.com/realtime"

    def connect(self):
        super().init(host=self.test_host, proxy_host='127.0.0.1', proxy_port=1087)

    def on_connected(self):
        print('websocket 连接成功')

    def on_packet(self, packet: dict):
        print(packet)
        if 'table' in packet:
            name = packet['table']
            print("出数据了")
            print(packet)

            if isinstance(packet["data"], list):
                # 这个是已经出来的数据
                for d in packet["data"]:
                    print(d)
        elif 'success' in packet:
            if packet['success']:
                print(f"{packet['request']['args']} is success")
        elif 'error' in packet:
            print("this is a error")
        elif 'info' in packet:
            _ = {
                "op": "subscribe",
                "args": "instrument:XBTUSD"
            }
            self.send_packet(_)


if __name__ == '__main__':
    bitmex = Bitmex()
    bitmex.connect()
    bitmex.start()
{% endcodeblock %}