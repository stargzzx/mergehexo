---
title: python | 线程间的通信
date: 2019-12-16 16:25:21
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
这个蛮重要的。

<!-- more -->

<br/>

# 直接使用共享内存

<br/>

python 的多线程效果很差，一般使用多进程，但是，多线程有一个好处就是，他们共享一个进程的内存信息。比如，在主程序中定义一个变量，那么线程中大家都会得到他。

所以，多线程同步，我们利用的也是这个特征。

你可以直接运行下面的代码，然后观察特征：

```python
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
```

<br/>

# condition实现线程通信「等待和唤醒」

<br/>

假设系统中有两个线程，这两个线程分别代表存款者和取钱者，现在假设系统有一种特殊的要求，即要求存款者和取钱者不断地重复存款、取钱的动作，而且要求每当存款者将钱存入指定账户后，取钱者就立即取出该笔钱。不允许存款者连续两次存钱，也不允许取钱者连续两次取钱。

为了实现这种功能，可以借助于 `Condition` 对象来保持协调。使用 Condition 可以让那些己经得到 Lock 对象却无法继续执行的线程释放 Lock 对象，Condition 对象也可以唤醒其他处于等待状态的线程。

threading.Condition() 创建一把资源锁（默认是Rlock）

将 Condition 对象与 Lock 对象组合使用，可以为每个对象提供多个等待集（wait-set）。因此，Condition 对象总是需要有对应的 Lock 对象。从 Condition 的构造器 `__init__(self, lock=None)` 可以看出，程序在创建 Condition 时可通过 lock 参数传入要绑定的 Lock 对象；如果不指定 lock 参数，在创建 Condition 时它会自动创建一个与之绑定的 Lock 对象。

Condition 类提供了如下几个方法：

- `acquire([timeout])/release()`：调用 Condition 关联的 Lock 的 acquire() 或 release() 方法。
- `wait([timeout])`：导致当前线程进入 Condition 的等待池等待通知并释放锁，直到其他线程调用该 Condition 的 notify() 或 notify_all() 方法来唤醒该线程。在调用该 wait() 方法时可传入一个 timeout 参数，指定该线程最多等待多少秒，wait() 必须在线程得到 Lock 后才能使用。
- `notify()`：唤醒在该 Condition 等待池中的单个线程并通知它，收到通知的线程将自动调用 acquire() 方法尝试加锁。如果所有线程都在该 Condition 等待池中等待，则会选择唤醒其中一个线程，选择是任意性的，Notify(n=1)，最多唤醒 n 个线程。
- `notify_all()`：唤醒在该 Condition 等待池中等待的所有线程并通知它们。

本例程序中，可以通过一个旗标来标识账户中是否已有存款，当旗标为 False 时，表明账户中没有存款，存款者线程可以向下执行，当存款者把钱存入账户中后，将旗标设为 True，并调用 Condition 的 notify() 或 notify_all() 方法来唤醒其他线程。

当存款者线程进入线程体后，如果旗标为 True，就调用 Condition 的 wait() 方法让该线程等待。当旗标为 True 时，表明账户中已经存入了钱，取钱者线程可以向下执行，当取钱者把钱从账户中取出后，将旗标设为 False，并调用 Condition 的 notify() 或 notify_all() 方法来唤醒其他线程；当取钱者线程进入线程体后，如果旗标为 False，就调用 wait() 方法让该线程等待。

本程序为 Account 类提供了 draw() 和 deposit() 两个方法，分别对应于该账户的取钱和存款操作。因为这两个方法可能需要并发修改 Account 类的 self.balance 成员变量的值，所以它们都使用 Lock 来控制线程安全。除此之外，这两个方法还使用了 Condition 的 wait() 和 notify_all() 来控制线程通信。

```python
import threading
class Account:
    # 定义构造器
    def __init__(self, account_no, balance):
        # 封装账户编号、账户余额的两个成员变量
        self.account_no = account_no
        self._balance = balance
        self.cond = threading.Condition()
        # 定义代表是否已经存钱的旗标
        self._flag = False
    # 因为账户余额不允许随便修改，所以只为self._balance提供getter方法
    def getBalance(self):
        return self._balance
    # 提供一个线程安全的draw()方法来完成取钱操作
    def draw(self, draw_amount):
        # 加锁,相当于调用Condition绑定的Lock的acquire()
        self.cond.acquire()
        try:
            # 如果self._flag为假，表明账户中还没有人存钱进去，取钱方法阻塞
            if not self._flag:
                self.cond.wait()
            else:
                # 执行取钱操作
                print(threading.current_thread().name
                    + " 取钱:" +  str(draw_amount))
                self._balance -= draw_amount
                print("账户余额为：" + str(self._balance))
                # 将标识账户是否已有存款的旗标设为False
                self._flag = False
                # 唤醒其他线程
                self.cond.notify_all()
        # 使用finally块来释放锁
        finally:
            self.cond.release()
    def deposit(self, deposit_amount):
        # 加锁,相当于调用Condition绑定的Lock的acquire()
        self.cond.acquire()
        try:
            # 如果self._flag为真，表明账户中已有人存钱进去，存钱方法阻塞
            if self._flag:            # ①
                self.cond.wait()
            else:
                # 执行存款操作
                print(threading.current_thread().name\
                    + " 存款:" +  str(deposit_amount))
                self._balance += deposit_amount
                print("账户余额为：" + str(self._balance))
                # 将表示账户是否已有存款的旗标设为True
                self._flag = True
                # 唤醒其他线程
                self.cond.notify_all()
        # 使用finally块来释放锁
        finally:
            self.cond.release()
```

关于为什么

self.cond.notify_all() 放在 self.cond.release() 前面，原因是，如果提前释放锁，由于 check interval 存在，可能会切换进程，导致，等待的进程永远无法被唤醒。

上面程序使用 Condition 的 wait() 和 notify_all() 方法进行控制，对存款者线程而言，当程序进入 deposit() 方法后，如果 self._flag 为 True，则表明账户中已有存款，程序调用 Condition 的 wait() 方法被阻塞；否则，程序向下执行存款操作，当存款操作执行完成后，系统将 self._flag 设为 True，然后调用 notify_all() 来唤醒其他被阻塞的线程。如果系统中有存款者线程，存款者线程也会被唤醒，但该存款者线程执行到 ① 号代码处时再次进入阻塞状态，只有执行 draw() 方法的取钱者线程才可以向下执行。同理，取钱者线程的运行流程也是如此。

程序中的存款者线程循环 100 次重复存款，而取钱者线程则循环 100 次重复取钱，存款者线程和取钱者线程分别调用 Account 对象的 deposit()、draw() 方法来实现。主程序可以启动任意多个“存款”线程和“取钱”线程，可以看到所有的“取钱”线程必须等“存款”线程存钱后才可以向下执行，而“存款”线程也必须等“取钱”线程取钱后才可以向下执行。主程序代码如下：

```python
import threading
import Account
#  定义一个函数，模拟重复max次执行取钱操作
def draw_many(account, draw_amount, max):
    for i in range(max):
        account.draw(draw_amount)
#  定义一个函数，模拟重复max次执行存款操作
def deposit_many(account, deposit_amount, max):
    for i in range(max):
        account.deposit(deposit_amount)
# 创建一个账户
acct = Account.Account("1234567" , 0)
# 创建、并启动一个“取钱”线程
threading.Thread(name="取钱者", target=draw_many,
    args=(acct, 800, 100)).start()
# 创建、并启动一个“存款”线程
threading.Thread(name="存款者甲", target=deposit_many,
    args=(acct , 800, 100)).start();
threading.Thread(name="存款者乙", target=deposit_many,
    args=(acct , 800, 100)).start()
threading.Thread(name="存款者丙", target=deposit_many,
    args=(acct , 800, 100)).start()
```

运行该程序，可以看到存款者线程、取钱者线程交替执行的情形，每当存款者向账户中存入 800 元之后，取钱者线程就立即从账户中取出这笔钱。存款完成后账户余额总是 800 元，取钱结束后账户余额总是 0 元。

    ...
    存款者丙 存款:800
    账户余额为：800
    取钱者 取钱:800
    账户余额为：0
    存款者丙 存款:800
    账户余额为：800
    取钱者 取钱:800
    账户余额为：0
    存款者甲 存款:800
    账户余额为：800
    ...

程序被堵塞

程序最后被阻塞无法继续向下执行。这是因为 3 个存款者线程共有 300 次尝试存钱操作，但 1 个取钱者线程只有 100 次尝试取钱操作，所以程序最后被阻塞。

阻塞并不是死锁，对于这种情况，取钱者线程已经执行结束，而存款者线程只是在等待其他线程来取钱而已，并不是等待其他线程释放同步监视器。不要把死锁和程序阻塞等同起来。

<br/>

# Python Queue队列实现线程通信

<br/>

queue 模块下提供了几个阻塞队列，这些队列主要用于实现线程通信。在 queue 模块下主要提供了三个类，分别代表三种队列，它们的主要区别就在于进队列、出队列的不同。

关于这三个队列类的简单介绍如下：
- queue.Queue(maxsize=0)：代表 FIFO（先进先出）的常规队列，maxsize 可以限制队列的大小。如果队列的大小达到队列的上限，就会加锁，再次加入元素时就会被阻塞，直到队列中的元素被消费。如果将 maxsize 设置为 0 或负数，则该队列的大小就是无限制的。

- queue.LifoQueue(maxsize=0)：代表 LIFO（后进先出）的队列，与 Queue 的区别就是出队列的顺序不同。

- PriorityQueue(maxsize=0)：代表优先级队列，优先级最小的元素先出队列。

这三个队列类的属性和方法基本相同， 它们都提供了如下属性和方法：

- Queue.qsize()：返回队列的实际大小，也就是该队列中包含几个元素。
- Queue.empty()：判断队列是否为空。
- Queue.full()：判断队列是否已满。
- Queue.put(item, block=True, timeout=None)：向队列中放入元素。如果队列己满，且 block 参数为 True（阻塞），当前线程被阻塞，timeout 指定阻塞时间，如果将 timeout 设置为 None，则代表一直阻塞，直到该队列的元素被消费；如果队列己满，且 block 参数为 False（不阻塞），则直接引发 queue.FULL 异常。
- Queue.put_nowait(item)：向队列中放入元素，不阻塞。相当于在上一个方法中将 block 参数设置为 False。
- Queue.get(item, block=True, timeout=None)：从队列中取出元素（消费元素）。如果队列已满，且 block 参数为 True（阻塞），当前线程被阻塞，timeout 指定阻塞时间，如果将 timeout 设置为 None，则代表一直阻塞，直到有元素被放入队列中； 如果队列己空，且 block 参数为 False（不阻塞），则直接引发 queue.EMPTY 异常。
- Queue.get_nowait(item)：从队列中取出元素，不阻塞。相当于在上一个方法中将 block 参数设置为 False。

下面以普通的 Queue 为例介绍阻塞队列的功能和用法。首先用一个最简单的程序来测试 Queue 的 put() 和 get() 方法。

```python
import queue
# 定义一个长度为2的阻塞队列
bq = queue.Queue(2)
bq.put("Python")
bq.put("Python")
print("1111111111")
bq.put("Python")  # ① 阻塞线程
print("2222222222")
```

在掌握了 Queue 阻塞队列的特性之后，在下面程序中就可以利用 Queue 来实现线程通信了。

与此类似的是，在 Queue 已空的情况下，程序使用 get() 方法尝试取出元素将会阻塞线程。

```python
import threading
import time
import queue
def product(bq):
    str_tuple = ("Python", "Kotlin", "Swift")
    for i in range(99999):
        print(threading.current_thread().name + "生产者准备生产元组元素！")
        time.sleep(0.2);
        # 尝试放入元素，如果队列已满，则线程被阻塞
        bq.put(str_tuple[i % 3])
        print(threading.current_thread().name \
            + "生产者生产元组元素完成！")
def consume(bq):
    while True:
        print(threading.current_thread().name + "消费者准备消费元组元素！")
        time.sleep(0.2)
        # 尝试取出元素，如果队列已空，则线程被阻塞
        t = bq.get()
        print(threading.current_thread().name \
            + "消费者消费[ %s ]元素完成！" % t)
# 创建一个容量为1的Queue
bq = queue.Queue(maxsize=1)
# 启动3个生产者线程
threading.Thread(target=product, args=(bq, )).start()
threading.Thread(target=product, args=(bq, )).start()
threading.Thread(target=product, args=(bq, )).start()
# 启动一个消费者线程
threading.Thread(target=consume, args=(bq, )).start()
```

上面程序启动了三个生产者线程向 Queue 队列中放入元素，启动了一个消费者线程从 Queue 队列中取出元素。本程序中 Queue 队列的大小为 1，因此三个生产者线程无法连续放入元素，必须等待消费者线程取出一个元素后，其中的一个生产者线程才能放入一个元素。

从运行结果中可以看出，三个生产者线程都想向 Queue 中放入元素，但只要其中一个生产者线程向该队列中放入元素之后，其他生产者线程就必须等待，等待消费者线程取出 Queue 队列中的元素。

<br/>

# Python Event实现线程通信

<br/>

 Event 是一种非常简单的线程通信机制，一个线程发出一个 Event，另一个线程可通过该 Event 被触发。

Event 本身管理一个内部旗标，程序可以通过 Event 的 set() 方法将该旗标设置为 True，也可以调用 clear() 方法将该旗标设置为 False。程序可以调用 wait() 方法来阻塞当前线程，直到 Event 的内部旗标被设置为 True。

Event 提供了如下方法：

- is_set()：该方法返回 Event 的内部旗标是否为True。
- set()：该方法将会把 Event 的内部旗标设置为 True，并唤醒所有处于等待状态的线程。
- clear()：该方法将 Event 的内部旗标设置为 False，通常接下来会调用 wait() 方法来阻塞当前线程。
- wait(timeout=None)：该方法会阻塞当前线程。

下面程序示范了 Event 最简单的用法：

```python
import threading
import time
event = threading.Event()
def cal(name):
    # 等待事件，进入等待阻塞状态
    print('%s 启动' % threading.currentThread().getName())
    print('%s 准备开始计算状态' % name)
    event.wait()    # ①
    # 收到事件后进入运行状态
    print('%s 收到通知了.' % threading.currentThread().getName())
    print('%s 正式开始计算！'% name)
# 创建并启动两条，它们都会①号代码处等待
threading.Thread(target=cal, args=('甲', )).start()
threading.Thread(target=cal, args=("乙", )).start()
time.sleep(2)    #②
print('------------------')
# 发出事件
print('主线程发出事件')
event.set()
```

运行上面程序，将看到如下输出结果：

    Thread-1 启动
    甲 准备开始计算状态
    Thread-2 启动
    乙 准备开始计算状态
    ------------------
    主线程发出事件
    Thread-1 收到通知了.
    Thread-2 收到通知了.
    甲 正式开始计算！
    乙 正式开始计算！

上面程序还没有使用 Event 的内部旗标，如果结合 Event 的内部旗标，同样可实现前面的 Account 的生产者-消费者效果：存钱线程（生产者）存钱之后，必须等取钱线程（消费者）取钱之后才能继续向下执行。

>Event 实际上优点类似于 Condition 和旗标的结合体，但 Event 本身并不带 Lock 对象，因此如果要实现线程同步，还需要额外的 Lock 对象。

下面是使用 Event 改写后的 Account：

```python
import threading
class Account:
    # 定义构造器
    def __init__(self, account_no, balance):
        # 封装账户编号、账户余额的两个成员变量
        self.account_no = account_no
        self._balance = balance
        self.lock = threading.Lock()
        self.event = threading.Event()
    # 因为账户余额不允许随便修改，所以只为self._balance提供getter方法
    def getBalance(self):
        return self._balance
    # 提供一个线程安全的draw()方法来完成取钱操作
    def draw(self, draw_amount):
        # 加锁
        self.lock.acquire()
        # 如果Event内部旗标为True，表明账户中已有人存钱进去
        if self.event.is_set():
            # 执行取钱操作
            print(threading.current_thread().name
                + " 取钱:" +  str(draw_amount))
            self._balance -= draw_amount
            print("账户余额为：" + str(self._balance))
            # 将Event内部旗标设为False
            self.event.clear()
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
        else:
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
    def deposit(self, deposit_amount):
        # 加锁
        self.lock.acquire()
        # 如果Event内部旗标为False，表明账户中还没有人存钱进去
        if not self.event.is_set():
            # 执行存款操作
            print(threading.current_thread().name\
                + " 存款:" +  str(deposit_amount))
            self._balance += deposit_amount
            print("账户余额为：" + str(self._balance))
            # 将Event内部旗标设为True
            self.event.set()
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
        else:
            # 释放加锁
            self.lock.release()
            # 阻塞当前线程阻塞
            self.event.wait()
```