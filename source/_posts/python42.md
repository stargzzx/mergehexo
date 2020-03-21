---
title: python threading 多线程
date: 2018-12-20 09:44:41
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- python 第三方库
- python
- threading
---
如题。

<!-- more -->

## 基础用法

```python
import threading
def test():
    print(threading.active_count())
		# 输出有多少个进程被激活
			# 1
    print(threading.enumerate())
		# 输出被激活进程的名字
			# [<_MainThread(MainThread, started 11792)>]
    print(threading.current_thread())
		# 输出当前正在使用哪个进程
			# <_MainThread(MainThread, started 11792)>
if __name__ == '__main__':
    test()
```

## 定义一个线程

	added_thread = threading.Thread(target=thread_job)
	
这段代码是讲，这是定义一个线程，里面的参数 target 就是要执行的代码段，也就是我们定义的那个线程要执行的内容

定义完之后，我们还得要执行

	added_thread.start()
	
```python
import threading
def thread_job():
    print("this is a test %s" % threading.current_thread())
		# this is a test <Thread(Thread-1, started 7412)>
def test():
    added_thread = threading.Thread(target=thread_job)
    added_thread.start()
    print(threading.active_count())
		# 2
    print(threading.enumerate())
		# [<_MainThread(MainThread, started 2396)>, <Thread(Thread-1, started 7412)>]
    print(threading.current_thread())
		# <_MainThread(MainThread, started 2396)>
if __name__ == '__main__':
    test()
```

## join 功能

演示一下线程间的异步

```python
import threading
import time
def thread_job():
    print("T1 START")
    time.sleep(1)
    print("T1 END")
def test():
    added_thread = threading.Thread(target=thread_job,name="T1")
    added_thread.start()
    print("all done")

if __name__ == '__main__':
    test()
	
	# 输出结果如下
	# T1 START
	# all done
	# T1 END
```

join 的作用，是必须先把目标线程运行完之后，再运行下面的代码，这个可多用于数据之间的传输等待

```python
import threading
import time
def thread_job():
    print("T1 START")
    time.sleep(1)
    print("T1 END")
def test():
    added_thread = threading.Thread(target=thread_job,name="T1")
    added_thread.start()
    added_thread.join()
    print("all done")

if __name__ == '__main__':
    test()
	
	# T1 START
	# T1 END
	# all done
```

多个线程间的演示

```python
import threading
import time
def thread_job():
    print("T1 START")
    time.sleep(1)
    print("T1 END")

def T2_job():
    print("T2 START")
    print("T2 END")
def test():
    added_thread = threading.Thread(target=thread_job,name="T1")
    thread2 = threading.Thread(target=T2_job,name="T2")
    added_thread.start()
    thread2.start()
    print("all done")

if __name__ == '__main__':
    test()
	
	# T1 START
	# T2 START
	# all done
	# T2 END
	# T1 END
```

三种加 join 的表现方式

```python
import threading
import time
def thread_job():
    print("T1 START")
    time.sleep(1)
    print("T1 END")

def T2_job():
    print("T2 START")
    print("T2 END")
def test():
    added_thread = threading.Thread(target=thread_job,name="T1")
    thread2 = threading.Thread(target=T2_job,name="T2")
    added_thread.start()
    thread2.start()
    added_thread.join()
    print("all done")

if __name__ == '__main__':
    test()

	# T1 START
	# T2 START
	# T2 END
	# T1 END
	# all done
```

在这个 join 中，我们给时间最长的线程加上 join 后，只是影响了主线程，而没有影响其他的另添加线程。

```python
import threading
import time
def thread_job():
    print("T1 START")
    time.sleep(1)
    print("T1 END")

def T2_job():
    print("T2 START")
    print("T2 END")
def test():
    added_thread = threading.Thread(target=thread_job,name="T1")
    thread2 = threading.Thread(target=T2_job,name="T2")
    added_thread.start()
    thread2.start()
    thread2.join()
    print("all done")

if __name__ == '__main__':
    test()

	# T1 START
	# T2 START
	# T2 END
	# all done
	# T1 END
```

```python
import threading
import time
def thread_job():
    print("T1 START")
    time.sleep(1)
    print("T1 END")

def T2_job():
    print("T2 START")
    print("T2 END")
def test():
    added_thread = threading.Thread(target=thread_job,name="T1")
    thread2 = threading.Thread(target=T2_job,name="T2")
    added_thread.start()
    thread2.start()
    added_thread.join()
    thread2.join()
    print("all done")

if __name__ == '__main__':
    test()
	# T1 START
	# T2 START
	# T2 END
	# T1 END
	# all done
```

## Queue 功能

多线程那些功能是没有返回值的，所以我们要将它运算的结果放在队列中，然后在主线程中拿出来再进行加工。

```python
import threading
import time
from queue import Queue

def job(l,q):
    for i in range(len(l)):
        l[i] = l[i] ** 2
    q.put(l)

def multithreading(data):
    q = Queue()
    threads = []
    for i in range(4):
        t = threading.Thread(target=job,args=(data[i],q))
        t.start()
        threads.append(t)
    results = []
    for _ in range(4):
        results.append(q.get())
        # q.get()  q 只能拿出一个，还是按照顺序拿出
    print(results)

if __name__ == '__main__':
    data = [[1,2,3],[3,4,5],[4,4,4],[5,5,5]]
    multithreading(data)
```

## 不一定有效果

python 的多线程并不是一个工作同时给很多个线程同时做，而是，python 有一个自带的程序，这个程序叫做 GIL ，GIL 可以将某一个线程锁住，然后，达到多线程的效果。只能让同一时间同一线程运行，通过不断的切换，让你看见多线程的效果。

```python
import threading
from queue import Queue
import copy
import time

def job(l, q):
    res = sum(l)
    q.put(res)

def multithreading(l):
    q = Queue()
    threads = []
    for i in range(4):
        t = threading.Thread(target=job, args=(copy.copy(l), q), name='T%i' % i)
        t.start()
        threads.append(t)
    [t.join() for t in threads]
    total = 0
    for _ in range(4):
        total += q.get()
    print(total)

def normal(l):
    total = sum(l)
    print(total)

if __name__ == '__main__':
    l = list(range(1000000))
    s_t = time.time()
    normal(l*4)
    print('normal: ',time.time()-s_t)
    s_t = time.time()
    multithreading(l)
    print('multithreading: ', time.time()-s_t)


	# 1999998000000
	# normal:  0.12218308448791504
	# 1999998000000
	# multithreading:  0.20545268058776855
```

## lock

如果第一个线程的处理结果是第二个线程的参数，所以，我们需要一个锁，来锁住第一个线程，然后等第一个线程处理完之后，在开始第二个。

```python
import threading
import time
def job1():
    global A
    for i in range(10):
        A += 1
        print('job1', A)
        time.sleep(0.1)

def job2():
    global A
    for i in range(10):
        A += 10
        print('job2', A)

if __name__ == '__main__':
    A = 0
    t1 = threading.Thread(target=job1)
    t2 = threading.Thread(target=job2)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
	# job1 1
	# job2 11
	# job2 21
	# job2 31
	# job2 41
	# job2 51
	# job2 61
	# job2 71
	# job2 81
	# job2 91
	# job2 101
	# job1 102
	# job1 103
	# job1 104
	# job1 105
	# job1 106
	# job1 107
	# job1 108
	# job1 109
	# job1 110
```

```python
import threading
import time

def job1():
    global A, lock
    lock.acquire()
    for i in range(10):
        A += 1
        print('job1', A)
        time.sleep(0.1)
    lock.release()

def job2():
    global A, lock
    lock.acquire()
    for i in range(10):
        A += 10
        print('job2', A)
    lock.release()

if __name__ == '__main__':
    lock = threading.Lock()
    A = 0
    t1 = threading.Thread(target=job1)
    t2 = threading.Thread(target=job2)
    t1.start()
    t2.start()
    t1.join()
    t2.join()
	
	# job1 1
	# job1 2
	# job1 3
	# job1 4
	# job1 5
	# job1 6
	# job1 7
	# job1 8
	# job1 9
	# job1 10
	# job2 20
	# job2 30
	# job2 40
	# job2 50
	# job2 60
	# job2 70
	# job2 80
	# job2 90
	# job2 100
	# job2 110
```


## 对 python 多线程的理解

### 回答一

首先，我们需要知道的是，python 的多线程是假的！！！

如果你的代码是CPU密集型，多个线程的代码很有可能是线性执行的。所以这种情况下多线程是鸡肋，效率可能还不如单线程因为有context switch但是：如果你的代码是IO密集型，多线程可以明显提高效率。例如制作爬虫（我就不明白为什么Python总和爬虫联系在一起…不过也只想起来这个例子…），绝大多数时间爬虫是在等待socket返回数据。这个时候C代码里是有release GIL的，最终结果是某个线程等待IO的时候其他线程可以继续执行。反过来讲：你就不应该用Python写CPU密集型的代码…效率摆在那里…

如果确实需要在CPU密集型的代码里用concurrent，就去用multiprocessing库。这个库是基于multi process实现了类multi thread的API接口，并且用pickle部分地实现了变量共享。

### 回答二

在介绍Python中的线程之前，先明确一个问题，Python中的多线程是假的多线程！ 

为什么这么说，我们先明确一个概念，全局解释器锁（GIL）。Python代码的执行由Python虚拟机（解释器）来控制。Python在设计之初就考虑要在主循环中，同时只有一个线程在执行，就像单CPU的系统中运行多个进程那样，内存中可以存放多个程序，但任意时刻，只有一个程序在CPU中运行。同样地，虽然Python解释器可以运行多个线程，只有一个线程在解释器中运行。对Python虚拟机的访问由全局解释器锁（GIL）来控制，正是这个锁能保证同时只有一个线程在运行。

在多线程环境中，Python虚拟机按照以下方式执行。

	1.设置GIL。
	2.切换到一个线程去执行。
	3.运行。
	4.把线程设置为睡眠状态。
	5.解锁GIL。
	6.再次重复以上步骤。

对所有面向I/O的（会调用内建的操作系统C代码的）程序来说，GIL会在这个I/O调用之前被释放，以允许其他线程在这个线程等待I/O的时候运行。

如果某线程并未使用很多I/O操作，它会在自己的时间片内一直占用处理器和GIL。

也就是说，I/O密集型的Python程序比计算密集型的Python程序更能充分利用多线程的好处。

我们都知道，比方我有一个4核的CPU，那么这样一来，在单位时间内每个核只能跑一个线程，然后时间片轮转切换。但是Python不一样，它不管你有几个核，单位时间多个核只能跑一个线程，然后时间片轮转。看起来很不可思议？但是这就是GIL搞的鬼。任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。通常我们用的解释器是官方实现的CPython，要真正利用多核，除非重写一个不带GIL的解释器。我们不妨做个试验：

```python
#coding=utf-8
from multiprocessing import Pool
from threading import Thread

from multiprocessing import Process


def loop():
    while True:
        pass

if __name__ == '__main__':

    for i in range(3):
        t = Thread(target=loop)
        t.start()

    while True:
        pass
```

我的电脑是4核，所以我开了4个线程，看一下CPU资源占有率：

![](/images/python/42_0.jpg)

我们发现CPU利用率并没有占满，大致相当于单核水平。

而如果我们变成进程呢？

我们改一下代码：

```python
#coding=utf-8
from multiprocessing import Pool
from threading import Thread

from multiprocessing import Process


def loop():
    while True:
        pass

if __name__ == '__main__':

    for i in range(3):
        t = Process(target=loop)
        t.start()

    while True:
        pass
```

![](/images/python/42_1.jpg)

结果直接飙到了100%，说明进程是可以利用多核的！


难道就如此？我们没有办法在Python中利用多核？当然可以！刚才的多进程算是一种解决方案，还有一种就是调用C语言的链接库。

对所有面向I/O的（会调用内建的操作系统C代码的）程序来说，GIL会在这个I/O调用之前被释放，以允许其他线程在这个线程等待I/O的时候运行。我们可以把一些 计算密集型任务用C语言编写，然后把.so链接库内容加载到Python中，因为执行C代码，GIL锁会释放，这样一来，就可以做到每个核都跑一个线程的目的！

可能有的小伙伴不太理解什么是计算密集型任务，什么是I/O密集型任务？计算密集型任务的特点是要进行大量的计算，消耗CPU资源，比如计算圆周率、对视频进行高清解码等等，全靠CPU的运算能力。这种计算密集型任务虽然也可以用多任务完成，但是任务越多，花在任务切换的时间就越多，CPU执行任务的效率就越低，所以，要最高效地利用CPU，计算密集型任务同时进行的数量应当等于CPU的核心数。计算密集型任务由于主要消耗CPU资源，因此，代码运行效率至关重要。

Python这样的脚本语言运行效率很低，完全不适合计算密集型任务。对于计算密集型任务，最好用C语言编写。第二种任务的类型是IO密集型，涉及到网络、磁盘IO的任务都是IO密集型任务，这类任务的特点是CPU消耗很少，任务的大部分时间都在等待IO操作完成（因为IO的速度远远低于CPU和内存的速度）。

对于IO密集型任务，任务越多，CPU效率越高，但也有一个限度。常见的大部分任务都是IO密集型任务，比如Web应用。IO密集型任务执行期间，99%的时间都花在IO上，花在CPU上的时间很少，因此，用运行速度极快的C语言替换用Python这样运行速度极低的脚本语言，完全无法提升运行效率。对于IO密集型任务，最合适的语言就是开发效率最高（代码量最少）的语言，脚本语言是首选，C语言最差。综上，Python多线程相当于单核多线程，多线程有两个好处：CPU并行，IO并行，单核多线程相当于自断一臂。所以，在Python中，可以使用多线程，但不要指望能有效利用多核。如果一定要通过多线程利用多核，那只能通过C扩展来实现，不过这样就失去了Python简单易用的特点。不过，也不用过于担心，Python虽然不能利用多线程实现多核任务，但可以通过多进程实现多核任务。多个Python进程有各自独立的GIL锁，互不影响。
