---
title: 事件驱动的一个简单的小demo
date: 2020-01-01 20:02:00
categories:
- 架构
- 事件驱动
tags:
- EDA
- 事件驱动
---
这个例子是由别人写的，但是，对于我理解事件驱动来说至关重要。

<!-- more -->

我们先来笼统地讲一下，事件驱动究竟是怎样的。

首先，我们以一个生活中的例子。有一家出版社，有几个消费者订阅了这个杂志，当，有新的杂志出来的时候，就会把这个新杂志发送给订阅了的消费者，当消费者拿到杂志开始阅读。

在这个例子中，我们要明确的是，消费者并不是一直轮询等待杂志过来，而是可以做其他的事，当有杂志来的时候，他自己自助的选择看杂志。

而，杂志社和消费者之间也只有订阅的关系。杂志社不会管消费者是男是女，是高是瘦，它所管的仅仅只是订阅的消费者。

所以，事件驱动是一个非常好的降耦合思想。杂志社只管生产杂志，消费者只管看杂志或者其它。

问题是，当杂志社生产出来杂志，是谁，投递到消费者手中。

我们姑且是一个快递小哥，当，杂志社生产出杂志后，就将其发给快递小哥，而快递小哥根据订阅名单，将杂志发给消费者。

这个例子中，其事件是 订阅。

对于程序要想实现上面的逻辑，我们首先需要有一个能够持续不断运行的进程，这个进程是一个引擎般的存在，因为这个引擎要接受各个对象发来的事件，然后找到相应的对象去触发事件。

这个引擎就相当于例子中的快递小哥。

直接上一下程序。

<br/>

# 目录结构

<br/>

![](/images/EDA/1_0.png)

<br/>

# 代码详解

<br/>

## Event.py

这个文件仅仅只是事件对象，你可以在这个文件中定义各种事件类型。

```python
class Event(object):
    # 事件对象
    def __init__(self, type=None):
        self.type = type
        self.dict = {}
```

## Constant.py

这个文件是你的各种事件名字。

```python
EVENT_ARTICAL = "Event_Artical"
```

## EventEngine.py

这个是事件引擎，相当于快递小哥的存在，是最核心的代码。

```python
from multiprocessing import Process, Queue


class EventEngine(object):
    # 初始化事件事件驱动引擎
    def __init__(self):
        # 保存事件列表
        self.__eventQueue = Queue()
        # 引擎开关
        self.__active = False
        # 事件处理字典{'event1': [handler1,handler2] , 'event2':[handler3, ...,handler4]}
        self.__handlers = {}
        # 保存事件处理进程池
        self.__processPool = []
        # 事件引擎主进程
        self.__mainProcess = Process(target=self.run)

    # 执行事件循环
    def run(self):
        while self.__active:
            # 事件队列非空
            if not self.__eventQueue.empty():
                # 获取队列中的事件 超时1秒
                event = self.__eventQueue.get(block=True, timeout=1)
                # 执行事件
                self.__process(event)
            else:
                # print('无任何事件')
                pass

    # 执行事件
    def __process(self, event):
        if event.type in self.__handlers:
            for handler in self.__handlers[event.type]:
                # 开一个进程去异步处理
                p = Process(target=handler, args=(event,))
                # 保存到进程池
                self.__processPool.append(p)
                p.start()

    # 开启事件引擎
    def start(self):
        self.__active = True
        self.__mainProcess.start()

    # 暂停事件引擎
    def stop(self):
        """停止"""
        # 将事件管理器设为停止
        self.__active = False
        # 等待事件处理进程退出
        for p in self.__processPool:
            p.join()
        self.__mainProcess.join()

    # 终止事件引擎
    def terminate(self):
        self.__active = False
        # 终止所有事件处理进程
        for p in self.__processPool:
            p.terminate()
        self.__mainProcess.join()

    # 注册事件
    def register(self, type, handler):
        """注册事件处理函数监听"""
        # 尝试获取该事件类型对应的处理函数列表，若无则创建
        try:
            handlerList = self.__handlers[type]
        except KeyError:
            handlerList = []
            self.__handlers[type] = handlerList

        # 若要注册的处理器不在该事件的处理器列表中，则注册该事件
        if handler not in handlerList:
            handlerList.append(handler)

    def unregister(self, type, handler):
        """注销事件处理函数监听"""
        # 尝试获取该事件类型对应的处理函数列表，若无则忽略该次注销请求
        try:
            handlerList = self.__handlers[type]

            # 如果该函数存在于列表中，则移除
            if handler in handlerList:
                handlerList.remove(handler)

            # 如果函数列表为空，则从引擎中移除该事件类型
            if not handlerList:
                del self.__handlers[type]
        except KeyError:
            pass

    def sendEvent(self, event):
        # 发送事件 像队列里存入事件
        self.__eventQueue.put(event)
```

## ListenerTypeOne.py

这个是消费者 1

```python
# 监听器 订阅者
class ListenerTypeOne:
    def __init__(self, username):
        self.__username = username

    # 监听器的处理函数 读文章
    def ReadArtical(self, event):
        print(u'%s 收到新文章' % self.__username)
        print(u'%s 正在阅读新文章内容：%s' % (self.__username, event.dict["artical"]))
```

## ListenerTypeTwo.py

这个是第二种消费者，之所以，有这个消费者是因为这个消费者会 sleep 3 秒，相当于，这个消费者本身就要忙其他的事，然后才来看杂志。

```python
import time
class ListenerTypeTwo:
    def __init__(self, username):
        self.__username = username

    # 监听器的处理函数 读文章
    def ReadArtical(self, event):
        print(u'%s 收到新文章 睡3秒再看' % self.__username)
        time.sleep(3)
        print(u'%s 正在阅读新文章内容：%s' % (self.__username, event.dict["artical"]))
```

## PublicAccounts.py

杂志社

```python
from Event import Event
from Constant import (
    EVENT_ARTICAL
)
class PublicAccounts:
    def __init__(self, eventManager):
        self.__eventManager = eventManager

    def writeNewArtical(self):
        # 事件对象，写了新文章
        event = Event(EVENT_ARTICAL)
        event.dict["artical"] = u'如何写出更优雅的代码\n'
        # 发送事件
        self.__eventManager.sendEvent(event)
        print(u'公众号发送新文章\n')
```

## main.py

执行总文件。

```python
# -*- coding: utf-8 -*-
from ListenerTypeOne import ListenerTypeOne
from ListenerTypeTwo import ListenerTypeTwo
from EventEngine import EventEngine
from Constant import (
    EVENT_ARTICAL
)
from PublicAccounts import PublicAccounts
__author__ = 'Jimmy'


# 测试
if __name__ == '__main__':

    listner1 = ListenerTypeOne("thinkroom")  # 订阅者1
    listner2 = ListenerTypeTwo("steve")  # 订阅者2

    ee = EventEngine()

    ee.register(EVENT_ARTICAL, listner1.ReadArtical)
    ee.register(EVENT_ARTICAL, listner2.ReadArtical)

    for i in range(0, 20):
        listner3 = ListenerTypeOne("Jimmy")  # 订阅者X
        ee.register(EVENT_ARTICAL, listner3.ReadArtical)

    ee.start()

    publicAcc = PublicAccounts(ee)
    publicAcc.writeNewArtical()
```
