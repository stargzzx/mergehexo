---
title: Python | 多线程之同步条件，信号量和队列
date: 2019-12-22 21:07:53
categories:
- multiprocess
tags:
- multiprocess
- 多进程
- 多线程
---
今天这篇文章大概介绍下python多线程中的同步条件Event,信号量(Semaphore)和队列(queue)。

<!-- more -->

<br/>

# 同步条件(Event)

<br/>

先说说为什么我们需要这个同步条件，我们的python多线程在执行task过程中，是相互竞争的，大家都可以先获取cpu的执行权限，这就是问题所在的地方，每个线程都是独立运行且状态不可预测，但是我们想想如果我们的业务中需要根据情况来决定线程的执行顺序，也就是程序中的其他线程需要通过判断某个线程的状态来确定自己下一步的操作，这时候我们就需要使用threading库中的Event对象。 对象包含一个可由线程设置的信号标志,它允许线程等待某些事件的发生。

在 初始情况下,Event对象中的信号标志被设置为假，如果有线程等待一个Event对象, ,那么这个线程将会被一直阻塞直至该标志为真。

一个线程如果将一个Event对象的信号标志设置为真,它将唤醒所有等待这个Event对象的线程继续执行。

Event的方法如下：

	event.isSet()：返回event的状态值
	event.wait()：如果 event.isSet()==False，将阻塞线程触发event.wait()
	event.set()： 设置event的状态值为True，所有阻塞池的线程激活进入就绪状态， 等待执行
	event.clear()：恢复event的状态值为False

下面段代码例子：

首先我们描述一个场景

1.老师说这堂课我们要做测试卷子，做完才能放学

2.学生叫苦连天，啊啊啊啊啊啊

3.学生做试卷中

4.做完试卷放学回家

```python
import threading
import time

class Teacher(threading.Thread):
    def run(self):
        print("大家现在要考试")
        print(event.isSet())
        event.set()
        time.sleep(3)
        print("考试结束")
        print(event.isSet())
        event.set()
class Student(threading.Thread):
    def run(self):
        event.wait()
        print("啊啊啊啊啊啊")
        time.sleep(1)
        event.clear()
        event.wait()
        print("下课回家")

if __name__=="__main__":
    event=threading.Event()
    threads=[]
    for i in range(10):
        threads.append(Student())
    threads.append(Teacher())
    for t in threads:
        t.start()
    for t in threads:
        t.join()
```

我们来解释下上面代码的执行流程

1.模拟1个老师和10个学生，进行考试，我们需要的目的是学生线程要等待老师线程说完“大家现在考试”，然后学生线程去考试，之后老师线程说“考试结束”，学生线程放学回家，学生线程的执行与否取决于老师线程，所以这里用的Event

2.学生线程开始event.wait()，这个说明如果event如果一直不设置的话，学生线程就一直等待，等待一个event.set()操作

3.老师线程说完"大家现在要考试"，然后event.set()，执行event,设置完执行，学生线程就能够被唤醒继续执行下面的操作发出"啊啊啊啊啊啊"的叫苦连天

4.学生线程进行考试，并且执行event.clear()，清除event，因为他们在等老师说“考试结束”，之后他们在等老师线程的event.set()

5.老师线程执行event.set()，唤醒学生线程，然后下课回家.

最后结果是：

	大家现在要考试
	False
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	啊啊啊啊啊啊
	考试结束
	False
	下课回家
	下课回家
	下课回家
	下课回家
	下课回家
	下课回家
	下课回家
	下课回家
	下课回家
	下课回家
	Process finished with exit code 0

不知道大家有没有看懂上面的一串解释，感觉还是比较乱，勉强看看。。。。

<br/>

# 信号量(Semaphore)

<br/>

信号量用来控制线程并发数的，Semaphore管理一个内置的计数 器，每当调用acquire()时-1，调用release()时+1。计数器不能小于0，当计数器为 0时，acquire()将阻塞线程至同步锁定状态，直到其他线程调用release()。其实就是控制最多几个线程可以操作同享资源。

```python
import threading
import time

semaphore = threading.Semaphore(5)

def func():
    if semaphore.acquire():
        print (threading.currentThread().getName() + '获取共享资源')
        time.sleep(2)
        semaphore.release()

for i in range(10)
  t1 = threading.Thread(target=func)
  t1.start()
```

上面一个简单的例子就是创建10个线程，让每次只让5个线程去执行func函数。

结果：5个线程一批一批的执行打印,中间停格2s

	Thread-1获取共享资源
	Thread-2获取共享资源
	Thread-3获取共享资源
	Thread-4获取共享资源
	Thread-5获取共享资源
	Thread-6获取共享资源
	Thread-8获取共享资源
	Thread-7获取共享资源
	Thread-9获取共享资源
	Thread-10获取共享资源

<br/>

# 队列(queue)

<br/>

Queue是python标准库中的线程安全的队列实现,提供了一个适用于多线程编程的先进先出的数据结构，即队列，用来在生产者和消费者线程之间的信息传递

说到线程安全，那么下面我们看看我们常用的list列表是不是线程安全的

```python
import threading,time

m=[1,2,3,4,5]
print(m[-1])

def remove_last():
    a=m[-1]
    time.sleep(1)
    m.remove(a)


t1=threading.Thread(target=remove_last)
t1.start()

t2=threading.Thread(target=remove_last)
t2.start()
```

结果

	Exception in thread Thread-2:
	Traceback (most recent call last):
	  File "C:\Users\aryin\AppData\Local\Programs\Python\Python37\lib\threading.py", line 917, in _bootstrap_inner
		self.run()
	  File "C:\Users\aryin\AppData\Local\Programs\Python\Python37\lib\threading.py", line 865, in run
		self._target(*self._args, **self._kwargs)
	  File "C:/Users/aryin/Desktop/mysite2/Event_Semaphore_queue.py", line 58, in remove_last
		m.remove(a)
	ValueError: list.remove(x): x not in list

上面的代码很简单就是开2个线程去上次队列M的最后一个数，按道理是最后m的数据会被remove完，但是结果却报错了，这是因为list不是线程安全的，线程一取了最后一个数据删除但是sleep 1秒的时候，线程2也拿到了相同的数，那么等线程1 remove这个数之后，线程2再去remove就会报错了

下面看看队列的常用方法

创建一个“队列”对象
import Queue
q = Queue.Queue(maxsize = 10)
Queue.Queue类即是一个队列的同步实现。队列长度可为无限或者有限。可通过Queue的构造函数的可选参数maxsize来设定队列长度。如果maxsize小于1就表示队列长度无限。

	将一个值放入队列中
	q.put(10)
	调用队列对象的put()方法在队尾插入一个项目。put()有两个参数，第一个item为必需的，为插入项目的值；第二个block为可选参数，默认为
	1。如果队列当前为空且block为1，put()方法就使调用线程暂停,直到空出一个数据单元。如果block为0，put方法将引发Full异常。

	将一个值从队列中取出
	q.get()
	调用队列对象的get()方法从队头删除并返回一个项目。可选参数为block，默认为True。如果队列为空且block为True，
	get()就使调用线程暂停，直至有项目可用。如果队列为空且block为False，队列将引发Empty异常。

	Python Queue模块有三种队列及构造函数:
	1、Python Queue模块的FIFO队列先进先出。   class queue.Queue(maxsize)
	2、LIFO类似于堆，即先进后出。               class queue.LifoQueue(maxsize)
	3、还有一种是优先级队列级别越低越先出来。        class queue.PriorityQueue(maxsize)

	此包中的常用方法(q = Queue.Queue()):
	q.qsize() 返回队列的大小
		这个命令在 MacBook下报错，要特别注意
	q.empty() 如果队列为空，返回True,反之False
	q.full() 如果队列满了，返回True,反之False
	q.full 与 maxsize 大小对应
	q.get([block[, timeout]]) 获取队列，timeout等待时间
	q.get_nowait() 相当q.get(False)
	非阻塞 q.put(item) 写入队列，timeout等待时间
	q.put_nowait(item) 相当q.put(item, False)
	q.task_done() 在完成一项工作之后，q.task_done() 函数向任务已经完成的队列发送一个信号
	q.join() 实际上意味着等到队列为空，再执行别的操作

队列(queue)一般会被用在生产者和消费者模型上。

生产者消费者模型：

为什么要使用生产者和消费者模式

在python线程中，生产者就是生产数据的线程，消费者就是消费数据的线程。在多线程开发当中，如果生产者处理速度很快，而消费者处理速度很慢，那么生产者就必须等待消费者处理完，才能继续生产数据。同样的道理，如果消费者的处理能力大于生产者，那么消费者就必须等待生产者。为了解决这个问题于是引入了生产者和消费者模式。

什么是生产者消费者模式

生产者消费者模式是通过一个容器来解决生产者和消费者的强耦合问题。生产者和消费者彼此之间不直接通讯，而通过阻塞队列来进行通讯，所以生产者生产完数据之后不用等待消费者处理，直接扔给阻塞队列，消费者不找生产者要数据，而是直接从阻塞队列里取，阻塞队列就相当于一个缓冲区，平衡了生产者和消费者的处理能力。

下面我们看看生产者消费者的代码，就拿大家常说的吃包子为例子吧

```python
import time,random
import queue,threading

q = queue.Queue()

def Producer(name):
  count = 0
  while count <10:
    print("制造包子ing")
    time.sleep(random.randrange(3))
    q.put(count)
    print('生产者 %s 生产了 %s 包子..' %(name, count))
    count +=1
    #q.task_done()
    #q.join()

def Consumer(name):
  count = 0
  while count <10:
    time.sleep(random.randrange(4))
    if not q.empty():
        data = q.get()
        #q.task_done()
        #q.join()
        print(data)
        print('消费者 %s 消费了 %s 包子...' %(name, data))
    else:
        print("包子吃完了")
    count +=1

c1 = threading.Thread(target=Producer, args=('小明',))
c2 = threading.Thread(target=Consumer, args=('小花',))
c3 = threading.Thread(target=Consumer, args=('小灰',))
c1.start()
c2.start()
c3.start()

c1.join()
c2.join()
c3.join()

print('结束')
```

结果大家可以有兴趣自己执行下看看，代码没什么难度，就是2个消费者和一个生产者的故事。

上面的代码我们还可以使用下面的方法来实现

```python
q.task_done() 在完成一项工作之后，q.task_done() 函数向任务已经完成的队列发送一个信号
q.join() 实际上意味着等到队列为空，再执行别的操作
import time,random
import queue,threading

q = queue.Queue()

def Producer(name):
  count = 0
  while count <10:
    print("制造包子ing")
    time.sleep(random.randrange(3))
    q.put(count)
    print('生产者 %s 生产了 %s 包子..' %(name, count))
    count +=1
    q.task_done()
    #q.join()

def Consumer(name):
  count = 0
  while count <10:
    time.sleep(random.randrange(4))
    data = q.get()
    #q.task_done()
    print('等待中')
    q.join()
    print('消费者 %s 消费了 %s 包子...' %(name, data))
    count +=1

c1 = threading.Thread(target=Producer, args=('小明',))
c2 = threading.Thread(target=Consumer, args=('小花',))
c3 = threading.Thread(target=Consumer, args=('小灰',))
c4 = threading.Thread(target=Consumer, args=('小天',))

c1.start()
c2.start()
c3.start()
c4.start()
```
