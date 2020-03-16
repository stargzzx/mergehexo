---
title: 用 websocket 连接 bitmex 交易所
date: 2020-01-01 20:29:39
categories:
- websocket
tags:
- network
- websocket
- websocket_client
---
直接贴一下代码吧。

<!-- more -->

```python
import websocket


def on_message(ws, message):  # 服务器有数据更新时，主动推送过来的数据
    print(message)


def on_error(ws, error):  # 程序报错时，就会触发on_error事件
    print(error)


def on_close(ws):
    print("Connection closed ……")


def on_open(ws):  # 连接到服务器之后就会触发on_open事件，这里用于send数据
    _ = '{"op": "subscribe","args": "instrument:XBTUSD"}'
    ws.send(_)


if __name__ == "__main__":
    websocket.enableTrace(True)
    ws = websocket.WebSocketApp("wss://www.bitmex.com/realtime",
                                on_message=on_message,
                                on_error=on_error,
                                on_close=on_close)
    ws.on_open = on_open
    ws.run_forever(http_proxy_host='127.0.0.1', http_proxy_port=1087)
```

里面的端口号 1087 是我 vpn 的端口号，各位可以根据自己的需要换。