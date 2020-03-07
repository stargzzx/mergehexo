---
title: 多进程或者线程之间的通信
date: 2019-12-16 16:25:21
categories:
- multiprocess
tags:
- multiprocess
- 多进程
- 多线程
---
这个蛮重要的。

<!-- more -->

这只是一个基础文章，以后我会出进阶文章的，敬请期待。

本次代码是依靠 python 来解决。

本次代码，我们会进行俩个方案的同步。

	多线程
		一个回调函数生成数据，一个线程拿数据
	多进程
		一个回调进程生成数据，一个进程拿数据

<br/>

# 多线程

<br/>

python 的多线程效果很差，一般使用多进程，但是，多线程有一个好处就是，他们共享一个进程的内存信息。比如，在主程序中定义一个变量，那么线程中大家都会得到他。

所以，多线程同步，我们利用的也是这个特征。

你可以直接运行下面的代码，然后观察特征：

{% codeblock %}
import time
import numpy as np
from threading import Thread
import matplotlib.pyplot as plt

arr = []
EEG_LENGTH = 100


def tt(f):
    while True:
        time.sleep(0.001)
        x = np.random.random()
        f(x)


def ttt(x):
    arr.append(x)


def proc2():
    plt.ion()
    figure = plt.figure(figsize=(10, 5))
    data = []
    i = 0
    x = np.linspace(0, 2, EEG_LENGTH)
    while True:
        print(len(arr))
        if len(arr) > 0:
            data.append(arr.pop())
        if len(data) == EEG_LENGTH:
            plt.cla()
            plt.plot(x, data)
            data = data[1:]
            plt.xticks([])
            plt.yticks([])
            plt.pause(0.00000001)



if __name__ == "__main__":
    p = Thread(target=proc2)
    p.start()
    tt(ttt)
{% endcodeblock %}

上面虽然可以实现，但是，创造的线程其效率并不高，比如，如果一个函数 0.001 秒生成数据，一个线程 0.001 秒或者更低，那么，大概率是生成函数更快一点。

python 的多线程需要双方的并发率不是很高的情况下使用。

假设并发很高，推荐使用多进程。

<br/>

# 多进程

<br/>

因为，多进程下，我们无法获得同一变量。

所以，我们要使用一个特殊的变量 from multiprocessing import Queue

用它在内存中划分一块区域，然后大家去调用它。

{% codeblock %}
import time
import numpy as np
import matplotlib.pyplot as plt
from multiprocessing import Queue
from multiprocessing import Process

q = Queue()  # 生成一个队列对象

arr = []
EEG_LENGTH = 100

plt.ion()


def tt(f):
    while True:
        time.sleep(0.01)
        x = np.random.random()
        f(x)


def ttt(x):
    q.put(x)
    print(q.qsize())


def proc2(q):
    data = []
    fig = plt.figure()
    while True:
        data.append(q.get())
        if len(data) > 200:
            x = np.linspace(0, 2, len(data))
            print(len(data))
            fig.clf()
            plt.plot(x, data)
            data = data[1:]
            plt.xticks([])
            plt.yticks([])
            plt.pause(0.00000001)
            fig.clf()


if __name__ == "__main__":
    t = Process(target=proc2, args=(q,))
    t.start()
    tt(ttt)
{% endcodeblock %}