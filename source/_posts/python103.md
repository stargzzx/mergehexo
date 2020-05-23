---
title: python | 定时调度
date: 2020-05-23 17:14:12
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
多线程中，python 有内置的定时调度。

<!-- more -->

<br/>

# Python Timer定时器

<br/>

Thread 类有一个 Timer子类，该子类可用于控制指定函数在特定时间内执行一次。例如如下程序：

```python
from threading import Timer
def hello():
    print("hello, world")
# 指定10秒后执行hello函数
t = Timer(10.0, hello)
t.start()
```

上面程序使用 Timer 控制 10s 后执行 hello 函数。

需要说明的是，Timer 只能控制函数在指定时间内执行一次，如果要使用 Timer 控制函数多次重复执行，则需要再执行下一次调度。

如果程序想取消 Timer 的调度，则可调用 Timer 对象的 cancel() 函数。例如，如下程序每 1s 输出一次当前时间：

```python
from threading import Timer
import time
# 定义总共输出几次的计数器
count = 0
def print_time():
    print("当前时间：%s" % time.ctime())
    global t, count
    count += 1
    # 如果count小于10，开始下一次调度
    if count < 10:
        t = Timer(1, print_time)
        t.start()
# 指定1秒后执行print_time函数
t = Timer(1, print_time)
t.start()
```

上面程序开始运行后，程序控制 1s 后执行 print_time() 函数。print_time() 函数中的代码会进行判断，如果 count 小于 10，程序再次使用 Timer 调度 1s 后执行 print_time() 函数，这样就可以控制 print_time() 函数多次重复执行。

在上面程序中，由于只有当 count 小于 10 时才会使用 Timer 调度 1s 后执行 print_time() 函数，因此该函数只会重复执行 10 次。

<br/>

# Python schedule任务调度及其用法

<br/>

使用 Timer 定时器有一个弊端，即只能控制线程在指定时间内执行一次任务，如果想实现每隔一段时间就执行一次，需要借助循环结构。

实际上，Python 还提供有一个更强大的、可用来定义执行任务调度的 sched 模块，该模块中含有一个 scheduler 类，可用来执行更复杂的任务调度。

scheduler 类常用的构造方法如下：

    scheduler(timefunc=time.monotonic, delayfunc=time.sleep)

可以向该构造方法中传入 2 个参数（当然也可以不提供，因为都有默认值），分别表示的含义如下：

- timefunc：指定生成时间戳的函数，默认使用 time.monotonic 来生成时间戳；
- delayfunc：在未到达指定时间前，通过该参数可以指定阻塞任务执行的函数，默认采用 time.sleep() 函数来阻塞程序。

另外，scheduler 类中还提供有一些方法。

|方法格式|功能|
|---|---|
|scheduler.enter(delay, priority, action, argument=(), kwargs={})|	在 time 规定的时间后，执行 action 参数指定的函数，其中 argument 和 kwargs 负责为 action 指定的函数传参，priority 参数执行要执行任务的等级，当同一时间点有多个任务需要执行时，等级越高（ priority 值越小）的任务会优先执行。该函数会返回一个 event，可用来取消该任务。|
|scheduler.cancel(event)|	取消 event 任务。注意，如果 event 参数执行的任务不存在，则会引发 ValueError 错误。|
|scheduler.run(blocking=True)|	运行所有需要调度的任务。如果调用该方法的 blocking 参数为 True，该方法将会阻塞线程，直到所有被调度的任务都执行完成。|

下面程序示范了 scheduler 类的用法。

```python
import threading
from sched import scheduler
def action(arg):
    print(arg)
#定义线程要调用的方法，*add可接收多个以非关键字方式传入的参数
def thread_action(*add):
    #创建任务调度对象
    sche = scheduler()
    #定义优先级
    i = 3
    for arc in add:
        # 指定1秒后执行action函数
        sche.enter(1, i, action,argument=(arc,))
        i = i - 1
    #执行所有调度的任务
    sche.run()
#定义为线程方法传入的参数
my_tuple = ("http://c.biancheng.net/python/",\
            "http://c.biancheng.net/shell/",\
            "http://c.biancheng.net/java/")
#创建线程
thread = threading.Thread(target = thread_action,args =my_tuple)
#启动线程
thread.start()
```

程序执行结果为：
    
    http://c.biancheng.net/java/
    http://c.biancheng.net/shell/
    http://c.biancheng.net/python/

>注意，以上输出结果是在执行程序 1 秒后逐个输出的。

上面程序中，创建了 thread 子线程去执行 thread_action() 函数，在该函数中使用 scheduler 类调度了 3 个任务，这 3 个任务都指定的是 1 秒后执行，其优先级分别为 3、2、1。

由于是在同一时间执行这 3 个任务，因此优先级的设定决定了谁先执行、谁后执行。显然优先级为 1 的任务优先执行，优先级为 3 的最后执行。因此上面程序执行结果中字符串的输出顺序恰好和 my_tuple 元组中的顺序是相反的。