---
title: python | GLI锁机制
date: 2018-12-19 22:12:37
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
- GIL
---
全局解释器锁，这个是理解多进程很重要的文章。

<!-- more -->

<br/>

# 什么是GLI？

<br/>

Python中的线程是操作系统的原生线程，Python虚拟机使用一个全局解释器锁（Global Interpreter Lock）来互斥线程对Python虚拟机的使用。为了支持多线程机制，一个基本的要求就是需要实现不同线程对共享资源访问的互斥，所以引入了GIL。由于GLI的存在，一个线程拥有了解释器的访问权之后，其他的所有线程都必须等待它释放解释器的访问权，即使这些线程的下一条指令并不会互相影响。在调用任何Python C API之前，都要先获得GIL。

<br/>

# GLI特点

<br/>

	缺点：多处理器退化为单处理器
	优点：避免大量的加锁解锁操作

<br/>

# GIL作用

<br/>

无论你启多少个线程，你有多少个cpu, Python在执行一个进程的时候会淡定的在同一时刻只允许一个线程运行。所以，python是无法利用多核CPU实现多线程的。这样，python对于计算密集型的任务开多线程的效率甚至不如串行(没有大量切换)，但是对于IO密集型的任务效率还是有显著提升的。

![](/images/multiprocess/3_0.png)

<br/>

# GLI 与 Lock

<br/>

GIL保护的是解释器级的数据，保护用户自己的数据则需要自己加锁处

![](/images/multiprocess/3_1.png)

<br/>

# GIL与多线程

<br/>

python解释器因为GIL的存在，在同一时刻同一进程中只有一个线程被执行。如何解决这一问题
首先明确一下三点：

	cpu到底是用来做计算的，还是用来做I/O的？
	多cpu，意味着可以有多个核并行完成计算，所以多核提升的是计算性能
	每个cpu一旦遇到I/O阻塞，仍然需要等待，所以多核对I/O操作没什么用处

举例：一个工人相当于cpu，此时计算相当于工人在干活，I/O阻塞相当于为工人干活提供所需原材料的过程，工人干活的过程中若没有原材料，则工人干活的过程需要停止，直到等待原材料的到来。如果你的工厂干的大多数任务都要有准备原材料的过程（I/O密集型），则有再多的工人，意义也不大，还不如一个人，在等材料的过程然后让工人去做别的事情，反过来讲，如果你的工厂原材料都齐全，那当然是工人越多，效率越高。由此可以得出结论：对计算来说，cpu越多越好，但是对于I/O来说，再多的cpu也没用当然对运行一个程序来说，随着cpu的增多执行效率肯定会有所提高（不管提高幅度多大，总会有所提高），这是因为一个程序基本上不会是纯计算或者纯I/O，所以我们只能相对的去看一个程序到底是计算密集型还是I/O密集型，从而进一步分析python的多线程到底有无用武之地.

<br/>

# 多进程计算密集型

<br/>

```python
from multiprocessing import Process
from threading import Thread
import os,time

def work():
    res=0
    for i in range(100000000):
        res*=i


if __name__ == '__main__':
    l=[]
    print(os.cpu_count()) #本机为4核
    start=time.time()
    for i in range(4):
        p=Process(target=work) #耗时5s多
        p=Thread(target=work) #耗时18s多
        l.append(p)
        p.start()
    for p in l:
        p.join()
    stop=time.time()
    print('run time is %s' %(stop-start))
```

<br/>

#  多线程IO密集型

<br/>

```python
from multiprocessing import Process
from threading import Thread
import threading
import os,time

def work():
    time.sleep(2)
    print('===>')

if __name__ == '__main__':
    l=[]
    print(os.cpu_count()) #本机为4核
    start=time.time()
    for i in range(400):
        # p=Process(target=work) #耗时12s多,大部分时间耗费在创建进程上
        p=Thread(target=work) #耗时2s多
        l.append(p)
        p.start()
    for p in l:
        p.join()
    stop=time.time()
    print('run time is %s' %(stop-start))
```

<br/>

# 并行与并发

<br/>

并行处理：（Parallel Processing）：是计算机系统中能同时执行两个或更多个处理的一种计算方法。并行处理可同时工作于同一程序的不同方面。并行处理的主要目的是节省大型和复杂问题的解决时间。

并发处理(concurrency Processing)：指一个时间段中有几个程序都处于已启动运行到运行完毕之间，且这几个程序都是在同一个处理机(CPU)上运行，但任一个时刻点上只有一个程序在处理机(CPU)上运行

ps:并发的关键是你有处理多个任务的能力，不一定要同时。并行的关键是你有同时处理多个任务的能力。因而并行是并发的子集.

![](/images/multiprocess/3_2.png)

<br/>

# 同步与异步

<br/>

同步：就是指一个进程在执行某个请求的时候，若该请求需要一段时间才能返回信息，那么这个进程将会一直等待下去，直到收到返回信息才继续执行下去

异步：是指进程不需要一直等下去，而是继续执行下面的操作，不管其他进程的状态。当有消息返回时系统会通知进程进行处理，这样可以提高执行的效率

ps:打电话时就是同步通信，发短息时就是异步通信。

<br/>

# Python GIL底层实现原理

<br/>

![](/images/multiprocess/3_3.gif)

上面这张图，就是 GIL 在 Python 程序的工作示例。其中，Thread 1、2、3 轮流执行，每一个线程在开始执行时，都会锁住 GIL，以阻止别的线程执行；同样的，每一个线程执行完一段后，会释放 GIL，以允许别的线程开始利用资源。

读者可能会问，为什么 Python 线程会去主动释放 GIL 呢？毕竟，如果仅仅要求 Python 线程在开始执行时锁住 GIL，且永远不去释放 GIL，那别的线程就都没有运行的机会。其实，CPython 中还有另一个机制，叫做间隔式检查（check_interval），意思是 CPython 解释器会去轮询检查线程 GIL 的锁住情况，每隔一段时间，Python 解释器就会强制当前线程去释放 GIL，这样别的线程才能有执行的机会。

注意，不同版本的 Python，其间隔式检查的实现方式并不一样。早期的 Python 是 100 个刻度（大致对应了 1000 个字节码）；而 Python 3 以后，间隔时间大致为 15 毫秒。当然，我们不必细究具体多久会强制释放 GIL，读者只需要明白，CPython 解释器会在一个“合理”的时间范围内释放 GIL 就可以了。

整体来说，每一个 Python 线程都是类似这样循环的封装，来看下面这段代码：

```python
for (;;) {
    if (--ticker < 0) {
        ticker = check_interval;   
        /* Give another thread a chance */
        PyThread_release_lock(interpreter_lock);
        /* Other threads may run now */   
        PyThread_acquire_lock(interpreter_lock, 1);
    }
    bytecode = *next_instr++;
    switch (bytecode) {
        /* execute the next instruction ... */
    }
}
```

从这段代码中可以看出，每个 Python 线程都会先检查 ticker 计数。只有在 ticker 大于 0 的情况下，线程才会去执行自己的代码。

<br/>

# Python GIL不能绝对保证线程安全

<br/>

注意，有了 GIL，并不意味着 Python 程序员就不用去考虑线程安全了，因为即便 GIL 仅允许一个 Python 线程执行，但别忘了 Python 还有 check interval 这样的抢占机制。

比如，运行如下代码：

```python
import threading
n = 0
def foo():
    global n
    n += 1
threads = []
for i in range(100):
    t = threading.Thread(target=foo)
    threads.append(t)
for t in threads:
    t.start()
for t in threads:
    t.join()
print(n)
```

执行此代码会发现，其大部分时候会打印 100，但有时也会打印 99 或者 98，原因在于 n+=1 这一句代码让线程并不安全。如果去翻译 foo 这个函数的字节码就会发现，它实际上是由下面四行字节码组成：

    >>> import dis
    >>> dis.dis(foo)
    LOAD_GLOBAL              0 (n)
    LOAD_CONST               1 (1)
    INPLACE_ADD
    STORE_GLOBAL             0 (n)

而这四行字节码中间都是有可能被打断的！所以，千万别以为有了 GIL 程序就不会产生线程问题，我们仍然需要注意线程安全。