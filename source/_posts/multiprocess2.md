---
title: python | 多线程间的运行
date: 2019-12-19 18:06:37
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
这篇文章介绍了python的多线程。

<!-- more -->

在看这篇文章之前，我建议你先去看看我的另一篇博文。

[python | 多进程间的运行](https://benpaodewoniu.github.io/2019/12/19/multiprocess1/)

threading 库可以在单独的线程中执行任何的在 Python 中可以调用的对象。你可以创建一个 Thread 对象并将你要执行的对象以 target 参数的形式提供给该对象。 下面是一个简单的例子：

```python
# Code to execute in an independent thread
import time
def countdown(n):
    while n > 0:
        print('T-minus', n)
        n -= 1
        time.sleep(5)

# Create and launch a thread
from threading import Thread
t = Thread(target=countdown, args=(10,))
t.start()
```

当你创建好一个线程对象后，该对象并不会立即执行，除非你调用它的 start() 方法（当你调用 start() 方法时，它会调用你传递进来的函数，并把你传递进来的参数传递给该函数）。Python中的线程会在一个单独的系统级线程中执行（比如说一个 POSIX 线程或者一个 Windows 线程），这些线程将由操作系统来全权管理。线程一旦启动，将独立执行直到目标函数返回。你可以查询一个线程对象的状态，看它是否还在执行：

```python
if t.is_alive():
    print('Still running')
else:
    print('Completed')
```

你也可以将一个线程加入到当前线程，并等待它终止：

	t.join()

Python解释器直到所有线程都终止前仍保持运行。对于需要长时间运行的线程或者需要一直运行的后台任务，你应当考虑使用后台线程。 例如：

	t = Thread(target=countdown, args=(10,), daemon=True)
	t.start()

后台线程无法等待，不过，这些线程会在主线程终止时自动销毁。 除了如上所示的两个操作，并没有太多可以对线程做的事情。你无法结束一个线程，无法给它发送信号，无法调整它的调度，也无法执行其他高级操作。如果需要这些特性，你需要自己添加。比如说，如果你需要终止线程，那么这个线程必须通过编程在某个特定点轮询来退出。你可以像下边这样把线程放入一个类中：

```python
class CountdownTask:
    def __init__(self):
        self._running = True

    def terminate(self):
        self._running = False

    def run(self, n):
        while self._running and n > 0:
            print('T-minus', n)
            n -= 1
            time.sleep(5)

c = CountdownTask()
t = Thread(target=c.run, args=(10,))
t.start()
c.terminate() # Signal termination
t.join()
```

如果线程执行一些像I/O这样的阻塞操作，那么通过轮询来终止线程将使得线程之间的协调变得非常棘手。比如，如果一个线程一直阻塞在一个I/O操作上，它就永远无法返回，也就无法检查自己是否已经被结束了。要正确处理这些问题，你需要利用超时循环来小心操作线程。 例子如下：

```python
class IOTask:
    def terminate(self):
        self._running = False

    def run(self, sock):
        # sock is a socket
        sock.settimeout(5)        # Set timeout period
        while self._running:
            # Perform a blocking I/O operation w/ timeout
            try:
                data = sock.recv(8192)
                break
            except socket.timeout:
                continue
            # Continued processing
            ...
        # Terminated
        return
```

由于全局解释锁（GIL）的原因，Python 的线程被限制到同一时刻只允许一个线程执行这样一个执行模型。所以，Python 的线程更适用于处理I/O和其他需要并发执行的阻塞操作（比如等待I/O、等待从数据库获取数据等等），而不是需要多处理器并行的计算密集型任务。

有时你会看到下边这种通过继承 Thread 类来实现的线程：

```python
from threading import Thread

class CountdownThread(Thread):
    def __init__(self, n):
        super().__init__()
        self.n = n
    def run(self):
        while self.n > 0:

            print('T-minus', self.n)
            self.n -= 1
            time.sleep(5)

c = CountdownThread(5)
c.start()
```

尽管这样也可以工作，但这使得你的代码依赖于 threading 库，所以你的这些代码只能在线程上下文中使用。上文所写的那些代码、函数都是与 threading 库无关的，这样就使得这些代码可以被用在其他的上下文中，可能与线程有关，也可能与线程无关。比如，你可以通过 multiprocessing 模块在一个单独的进程中执行你的代码：

```python
import multiprocessing
c = CountdownTask(5)
p = multiprocessing.Process(target=c.run)
p.start()
```

<br/>

# 知识点一

<br/>

当一个进程启动之后，会默认产生一个主线程，因为线程是程序执行流的最小单元，当设置多线程时，主线程会创建多个子线程，在python中，默认情况下（其实就是setDaemon(False)），主线程执行完自己的任务以后，就退出了，此时子线程会继续执行自己的任务，直到自己的任务结束，例子见下面一

<br/>

# 知识点二

<br/>

当我们使用setDaemon(True)方法，设置子线程为守护线程时，主线程一旦执行结束，则全部线程全部被终止执行，可能出现的情况就是，子线程的任务还没有完全执行结束，就被迫停止，例子见下面二。

<br/>

# 知识点三

<br/>

此时join的作用就凸显出来了，join所完成的工作就是线程同步，即主线程任务结束之后，进入阻塞状态，一直等待其他的子线程执行结束之后，主线程在终止，例子见下面三。

<br/>

# 知识点四

<br/>

join有一个timeout参数：

	当设置守护线程时，含义是主线程对于子线程等待timeout的时间将会杀死该子线程，最后退出程序。所以说，如果有10个子线程，全部的等待时间就是每个timeout的累加和。简单的来说，就是给每个子线程一个timeout的时间，让他去执行，时间一到，不管任务有没有完成，直接杀死。

	没有设置守护线程时，主线程将会等待timeout的累加和这样的一段时间，时间一到，主线程结束，但是并没有杀死子线程，子线程依然可以继续执行，直到子线程全部结束，程序退出。

<br/>

# Python多线程的默认情况

<br/>

```python
import threading
import time

def run():
    time.sleep(2)
    print('当前线程的名字是： ', threading.current_thread().name)
    time.sleep(2)


if __name__ == '__main__':

    start_time = time.time()

    print('这是主线程：', threading.current_thread().name)
    thread_list = []
    for i in range(5):
        t = threading.Thread(target=run)
        thread_list.append(t)

    for t in thread_list:
        t.start()

    print('主线程结束！' , threading.current_thread().name)
    print('一共用时：', time.time()-start_time)
```

其执行结果如下

	这是主线程： MainThread
	主线程结束！ MainThread
	一共用时： 0.002926349639892578
	当前线程的名字是：  Thread-1
	当前线程的名字是：  Thread-3
	当前线程的名字是：  Thread-2
	当前线程的名字是：  Thread-5
	当前线程的名字是：  Thread-4

关键点：

	我们的计时是对主线程计时，主线程结束，计时随之结束，打印出主线程的用时。

	主线程的任务完成之后，主线程随之结束，子线程继续执行自己的任务，直到全部的子线程的任务全部结束，程序结束。

<br/>

# 设置daemon：

<br/>

```python
import threading
import time
 
def run():
 
    time.sleep(2)
    print('当前线程的名字是： ', threading.current_thread().name)
    time.sleep(2)
 
 
if __name__ == '__main__':
 
    start_time = time.time()
 
    print('这是主线程：', threading.current_thread().name)
    thread_list = []
    for i in range(5):
        t = threading.Thread(target=run)
        thread_list.append(t)
 
    for t in thread_list:
        t.setDaemon(True)
        t.start()
 
    print('主线程结束了！' , threading.current_thread().name)
    print('一共用时：', time.time()-start_time)
```

	这是主线程： MainThread
	主线程结束了！ MainThread
	一共用时： 0.0020210742950439453

	非常明显的看到，主线程结束以后，子线程还没有来得及执行，整个程序就退出了。

<br/>

# 设置join

<br/>

```python
import threading
import time


def run():
    time.sleep(2)
    print('当前线程的名字是： ', threading.current_thread().name)
    time.sleep(2)


if __name__ == '__main__':

    start_time = time.time()

    print('这是主线程：', threading.current_thread().name)
    thread_list = []
    for i in range(5):
        t = threading.Thread(target=run)
        thread_list.append(t)

    for t in thread_list:
        t.setDaemon(True)
        t.start()

    for t in thread_list:
        t.join()

    print('主线程结束了！', threading.current_thread().name)
    print('一共用时：', time.time() - start_time)
```

运行结果如下：

	这是主线程： MainThread
	当前线程的名字是：  Thread-2
	当前线程的名字是：  Thread-1
	当前线程的名字是：  Thread-3
	当前线程的名字是：  Thread-5
	当前线程的名字是：  Thread-4
	主线程结束了！ MainThread
	一共用时： 4.003437519073486

	可以看到，主线程一直等待全部的子线程结束之后，主线程自身才结束，程序退出。

## 多线程中阻塞(join)使用误区详解

join( ) 阻塞主线程

	join() 作用为阻塞主线程,即在子线程未返回的时候,主线程等待其返回然后再继续执行

	join不能与start在循环里连用

以下为错误代码,代码创建了5个线程,然后用一个循环激活线程,激活之后令其阻塞主线程

	threads = [Thread() for i in range(5)]
	for thread in threads:
		 thread.start()
		 thread.join()

执行过程: 

	1. 第一次循环中,主线程通过start函数激活线程1,线程1进行计算 
	2. 由于start函数不阻塞主线程,在线程1进行运算的同时,主线程向下执行join函数 
	3. 执行join之后,主线程被线程1阻塞,在线程1返回结果之前,主线程无法执行下一轮循环 
	4. 线程1计算完成之后,解除对主线程的阻塞 
	5. 主线程进入下一轮循环,激活线程2并被其阻塞 

如此往复,可以看出,本来应该并发的五个线程,在这里变成了顺序队列,效率和单线程无异

join的正确用法：使用两个循环分别处理start和join函数，即可实现并发

	threads = [Thread() for i in range(5)]
	for thread in threads:
		thread.start()
	for thread in threads:
		thread.join()