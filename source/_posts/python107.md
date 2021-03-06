---
title: python | 进程间的通信
date: 2020-05-23 18:18:09
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
Python 提供了多种实现进程间通信的机制，主要有以下 2 种：
 
- Python multiprocessing 模块下的 Queue 类，提供了多个进程之间实现通信的诸多方法；
- Pipe，又被称为“管道”，常用于实现 2 个进程之间的通信，这 2 个进程分别位于管道的两端。

<!-- more -->

<br/>

# Queue实现进程间通信

<br/>

前面讲解了使用 Queue 模块中的 Queue 类实现线程间通信，但要实现进程间通信，需要使用 multiprocessing 模块中的 Queue 类。

简单的理解 Queue 实现进程间通信的方式，就是使用了操作系统给开辟的一个队列空间，各个进程可以把数据放到该队列中，当然也可以从队列中把自己需要的信息取走。

Queue 类提供了诸多实现进程间通信的方法

Python multiprocessing Queue 类常用方法

<br/>

|方法名|	功能|
|---|---|
|put(obj[,block=True[,timeout=None]])|	将 obj 放入队列，其中当 block 参数设为 True 时，一旦队列被写满，则代码就会被阻塞，直到有进程取走数据并腾出空间供 obj 使用。<br/>timeout 参数用来设置阻塞的时间，即程序最多在阻塞 timeout 秒之后，如果还是没有空闲空间，则程序会抛出 queue.Full 异常。|
|put_nowait(obj)|	该方法的功能等同于 put(obj, False)。|
|get([block=True,[timeout=None]])|	从队列中取数据并返回，当 block 为 True 且 timeout 为 None 时，该方法会阻塞当前进程，直到队列中有可用的数据。<br/>如果 block 设为 False，则进程会直接做取数据的操作，如果取数据失败，则抛出 queue.Empty 异常（这种情形下 timeout 参数将不起作用）。<br/>如果手动 timeout 秒数，则当前进程最多被阻塞 timeout 秒，如果到时依旧没有可用的数据取出，则会抛出 queue.Empty 异常。|
|get_nowait()|	该方法的功能等同于 get(False)。|
|empty()|	判断当前队列空间是否为空，如果为空，则该方法返回 True；反之，返回 False。|

下面程序演示了如何使用 Queue 类实现多进程之间的通信。

```python
import multiprocessing
def processFun(queue,name):
    print(multiprocessing.current_process().pid,"进程放数据：",name)
    #将 name 放入队列
    queue.put(name)
if __name__ == '__main__':
    # 创建进程通信的Queue
    queue = multiprocessing.Queue()
    # 创建子进程
    process = multiprocessing.Process(target=processFun, args=(queue,"http://c.biancheng.net/python/"))
    # 启动子进程
    process.start()
    #该子进程必须先执行完毕
    process.join()
    print(multiprocessing.current_process().pid,"取数据：")
    print(queue.get())
```

程序执行结果为：
    
    27100 进程放数据： http://c.biancheng.net/python/
    24188 取数据：
    http://c.biancheng.net/python/

<br/>

# Pipe实现进程间通信

<br/>

Pipe 直译过来的意思是“管”或“管道”，该种实现多进程编程的方式，和实际生活中的管（管道）是非常类似的。通常情况下，管道有 2 个口，而 Pipe 也常用来实现 2 个进程之间的通信，这 2 个进程分别位于管道的两端，一端用来发送数据，另一端用来接收数据。

使用 Pipe 实现进程通信，首先需要调用 multiprocessing.Pipe() 函数来创建一个管道。该函数的语法格式如下：

    conn1, conn2 = multiprocessing.Pipe( [duplex=True] )

其中，conn1 和 conn2 分别用来接收 Pipe 函数返回的 2 个端口；duplex 参数默认为 True，表示该管道是双向的，即位于 2 个端口的进程既可以发送数据，也可以接受数据，而如果将 duplex 值设为 False，则表示管道是单向的，conn1 只能用来接收数据，而 conn2 只能用来发送数据。

另外值得一提的是，conn1 和 conn2 都属于 PipeConnection 对象，它们还可以调用下列表所示的这些方法。

Pipe 对象可调用的方法

<br/>

|方法名|	功能|
|---|---|
|send(obj)|	发送一个 obj 给管道的另一端，另一端使用 recv() 方法接收。需要说明的是，该 obj 必须是可序列化的，如果该对象序列化之后超过 32MB，则很可能会引发 ValueError 异常。|
|recv()|	接收另一端通过 send() 方法发送过来的数据。|
|close()|	关闭连接。|
|poll([timeout])|	返回连接中是否还有数据可以读取。|
|send_bytes(buffer[,offset[,size]])|	发送字节数据。如果没有指定 offset、size 参数，则默认发送 buffer 字节串的全部数据；如果指定了 offset 和 size 参数，则只发送 buffer 字节串中从 offset 开始、长度为 size 的字节数据。通过该方法发送的数据，应该使用 recv_bytes() 或 recv_bytes_into 方法接收。|
|recv_bytes([maxlength])|	接收通过 send_bytes() 方法发送的数据，maxlength 指定最多接收的字节数。该方法返回接收到的字节数据。|
|recv_bytes_into(buffer[,offset])|	功能与 recv_bytes() 方法类似，只是该方法将接收到的数据放在 buffer 中。|

下面程序演示了如何使用 Pipe 管道实现 2 个进程之间通信：

```python
import multiprocessing
def processFun(conn,name):
    print(multiprocessing.current_process().pid,"进程发送数据：",name)
    conn.send(name)
if __name__ == '__main__':
    #创建管道
    conn1,conn2 = multiprocessing.Pipe()
    # 创建子进程
    process = multiprocessing.Process(target=processFun, args=(conn1,"http://c.biancheng.net/python/"))
    # 启动子进程
    process.start()
    process.join()
    print(multiprocessing.current_process().pid,"接收数据：")
    print(conn2.recv())
```

程序执行结果为：
    
    28904 进程发送数据： http://c.biancheng.net/python/
    28880 接收数据：
    http://c.biancheng.net/python/