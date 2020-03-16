---
title: python websocket获取实时数据的几种常见链接方式
date: 2019-12-23 21:45:57
categories:
- websocket
tags:
- network
- websocket
- websocket_client
---
这个是我看别人做的例子，很有启发，在这里我贴一下。

<!-- more -->

# 第一种

使用create_connection链接，需要pip install websocket-client (此方法不建议使用，链接不稳定，容易断，并且连接很耗时)

```python
import time
from websocket import create_connection
 
url = 'wss://i.cg.net/wi/ws'
while True: # 一直链接，直到连接上就退出循环
  time.sleep(2)
  try:
    ws = create_connection(url)
    print(ws)
    break
  except Exception as e:
    print('连接异常：', e)
    continue
while True: # 连接上，退出第一个循环之后，此循环用于一直获取数据
  ws.send('{"event":"subscribe", "channel":"btc_usdt.ticker"}')
  response = ws.recv()
  print(response)
```

# 第二种

运行效果很不错，很容易连接，获取数据的速度也挺快

```python
import json
from ws4py.client.threadedclient import WebSocketClient
 
 
class CG_Client(WebSocketClient):
 
  def opened(self):
    req = '{"event":"subscribe", "channel":"eth_usdt.deep"}'
    self.send(req)
 
  def closed(self, code, reason=None):
    print("Closed down:", code, reason)
 
  def received_message(self, resp):
    resp = json.loads(str(resp))
    data = resp['data']
    if type(data) is dict:
      ask = data['asks'][0]
      print('Ask:', ask)
      bid = data['bids'][0]
      print('Bid:', bid)
 
 
if __name__ == '__main__':
  ws = None
  try:
    ws = CG_Client('wss://i.cg.net/wi/ws')
    ws.connect()
    ws.run_forever()
  except KeyboardInterrupt:
    ws.close()
```

# 第三种

其实和第一种差不多，只不过换种写法而已，运行效果不理想，连接耗时，并且容易断

```python
import websocket
 
while True:
  ws = websocket.WebSocket()
  try:
    ws.connect("wss://i.cg.net/wi/ws")
    print(ws)
    break
  except Exception as e:
    print('异常：', e)
    continue
print('OK')
while True:
  req = '{"event":"subscribe", "channel":"btc_usdt.deep"}'
  ws.send(req)
  resp = ws.recv()
  print(resp)
```

# 第四种

运行效果也可以，run_forever里面有许多参数，需要自己设置

```python
import websocket
 
 
def on_message(ws, message): # 服务器有数据更新时，主动推送过来的数据
  print(message)
 
 
def on_error(ws, error): # 程序报错时，就会触发on_error事件
  print(error)
 
 
def on_close(ws):
  print("Connection closed ……")
 
 
def on_open(ws): # 连接到服务器之后就会触发on_open事件，这里用于send数据
  req = '{"event":"subscribe", "channel":"btc_usdt.deep"}'
  print(req)
  ws.send(req)
 
 
if __name__ == "__main__":
  websocket.enableTrace(True)
  ws = websocket.WebSocketApp("wss://i.cg.net/wi/ws",
                on_message=on_message,
                on_error=on_error,
                on_close=on_close)
  ws.on_open = on_open
  ws.run_forever(ping_timeout=30)
```
