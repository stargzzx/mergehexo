---
title: python | threading Local()
date: 2020-05-23 16:38:10
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
当多线程操作同一公有资源时，如果涉及到修改该资源的操作，为了避免数据不同步可能导致的错误，需要使用互斥锁机制。

其实，除非必须将多线程使用的资源设置为公共资源，Python threading 模块还提供了一种可彻底避免数据不同步问题的方法，即local() 函数。

<!-- more -->

使用 local() 函数创建的变量，可以被各个线程调用，但和公共资源不同，各个线程在使用 local() 函数创建的变量时，都会在该线程自己的内存空间中拷贝一份。这意味着，local() 函数创建的变量看似全局变量（可以被各个线程调用），但各线程调用的都是该变量的副本（各调用各的，之间并无关系）。

可以这么理解，使用 threading 模块中的 local() 函数，可以为各个线程创建完全属于它们自己的变量（又称线程局部变量）。正是由于各个线程操作的是属于自己的变量，该资源属于各个线程的私有资源，因此可以从根本上杜绝发生数据同步问题。

下面程序示范了 threading local() 函数的用法：

```python
import threading
# 创建全局ThreadLocal对象:
local = threading.local()
def process():
    # 3.获取当前线程关联的resource:
    res = local.resource
    print (res + "http://c.biancheng.net/python/")
def process_thread(res):
    # 1.为当前线程绑定ThreadLocal的resource:
    local.resource = res
    # 2.线程的调用函数不需要传递res了，可以通过全局变量local访问
    process()
t1 = threading.Thread(target=process_thread, args=('t1线程',))
t2 = threading.Thread(target=process_thread, args=('t2线程',))
t1.start()
t2.start()
```

程序执行结果为：
    
    t1线程http://c.biancheng.net/python/
    t2线程http://c.biancheng.net/python/

通过分析上面的样例，不难看出，使用 local() 函数的好处，至少有以下 2 点：

- 各个线程操作的都是自己的私有资源，不会涉及到数据同步问题；
- 由于 local() 函数的返回值位于全局作用域，无论在程序什么位置，都可以随时调用，很方便。

<br/>

# 总结

<br/>

无论是使用线程局部变量，还是使用互斥锁机制，其根本目的是为了解决多线程访问公共资源时可能发生的数据同步问题。互斥锁机制实现的出发点是，在各线程仍使用公共资源的前提下，想办法控制各个线程对该资源的同时访问；而线程局部变量则另辟蹊径，直接令多线程操作各自的私有资源，从根本上杜绝了同时访问所带来的数据同步问题。

需要说明的一点是，线程局部变量的解决方案，并不能完全替代互斥锁同步机制。同步机制是为了同步多个线程对公共资源的并发访问，是多个线程之间进行通信的有效方式；而线程局部变量则从根本上避免了多个钱程之间对共享资源（变量）的竞争。

那么，这两种解决方案该如何选择呢？简单来说，如果多线程之间需要共享资源（如多人操作同一银行账户的例子），就使用互斥锁同步机制；反之，如果仅是为了解决多线程之间的共享资源冲突，则推荐使用线程局部变量。