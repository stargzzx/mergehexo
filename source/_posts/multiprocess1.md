---
title: python | 多进程间的运行
date: 2019-12-19 17:58:20
categories:
- multiprocess
tags:
- multiprocess
- 多进程
- 多线程
---
多进程之间如何相互运行，这篇文章给你答案。

<!-- more -->

首先，我们要明白多进程可以分为两个阵营，一个是主进程，另一个是分进程，这一篇我们主要是想讨论一下关于主进程和分进程的寿命问题。

<br/>

# multiprocessing

<br/>

python中的多线程其实并不是真正的多线程，如果想要充分地使用多核CPU的资源，在python中大部分情况需要使用多进程。Python提供了非常好用的多进程包multiprocessing，只需要定义一个函数，Python会完成其他所有事情。借助这个包，可以轻松完成从单进程到并发执行的转换。multiprocessing支持子进程、通信和共享数据、执行不同形式的同步，提供了Process、Queue、Pipe、Lock等组件。

<br/>

# Process

<br/>

## 创建进程的类

__init__(self, group=None, target=None, name=None, args=(), kwargs={})

	group：进程所属组。基本不用 
	target：表示调用对象。 
	args：表示调用对象的位置参数元组。 
	name：别名 
	kwargs：表示调用对象的字典。

## 方法

is_alive()、join([timeout])、run()、start()、terminate()。其中，Process以start()启动某个进程。

## 属性

authkey、daemon（要通过start()设置）、exitcode(进程在运行时为None、如果为–N，表示被信号N结束）、name、pid。其中daemon是父进程终止后自动终止，且自己不能产生新进程，必须在start()之前设置。

## 停止

要停止一个进程实例,可以调用方法terminate

	p.terminate()

但是通过执行系统命令ps查看停止后的进程, 你会发现, 直接调用terminate方法停止的进程变成了一个僵尸进程(defunct), 只能等待主程序退出, 这个僵尸进程才会消失.

通过在terminate后添加一次调用join方法等待进程真正结束, 就能避免出现僵尸进程:

	p.join()

<br/>

# 一个简单的例子

<br/>

```python
import time
from multiprocessing import Process


def run_forever():
    while 1:
        print(time.time())
        time.sleep(2)


def main():
    p = Process(target=run_forever)
    p.start()
    print('start a process.')
    time.sleep(10)
    if p.is_alive:
        # stop a process gracefully
        p.terminate()
        print('stop process')
        p.join()


if __name__ == '__main__':
    main()
```

运行结果

	start a process.
	1576754064.1161447
	1576754066.1169126
	1576754068.1172283
	1576754070.117954
	1576754072.1185668
	stop process

创建子进程时，只需要传入一个执行函数和函数的参数，创建一个Process实例，并用其start()方法启动，这样创建进程比fork()还要简单。 join()方法表示等待子进程结束以后再继续往下运行，通常用于进程间的同步。

<br/>

# 注意： 

<br/>

在Windows上要想使用进程模块，就必须把有关进程的代码写在当前.py文件的if __name__ == ‘__main__’ :语句的下面，才能正常使用Windows下的进程模块。Unix/Linux下则不需要。

<br/>

# 关于 join()

<br/>

join()方法可以在当前位置阻塞主进程，带执行join()的进程结束后再继续执行主进程的代码逻辑。

```python
from multiprocessing import Process
import os
import time

def now():
    return str(time.strftime('%Y-%m-%d %H:%M:%S', time.localtime()))


def func_1(name):
    print(now() + ' Run child process %s (%s)...' % (name, os.getpid()))
    time.sleep(4)
    print(now() + ' Stop child process %s (%s)...\n' % (name, os.getpid()))


def func_2(name):
    print(now() + ' Run child process %s (%s)...' % (name, os.getpid()))
    time.sleep(8)
    print(now() + ' hello world!')
    print(now() + ' Stop child process %s (%s)...\n' % (name, os.getpid()))


if __name__ == '__main__':
    print ('Parent process %s.' % os.getpid())
    p1 = Process(target=func_1, args=('func_1',))
    p2 = Process(target=func_2, args=('func_2',))
    print (now() + ' Process start.')
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    print (now() + ' Process end .')
```

输出的内容

	Parent process 7080.
	2019-12-19 19:16:59 Process start.
	2019-12-19 19:16:59 Run child process func_1 (5360)...
	2019-12-19 19:16:59 Run child process func_2 (19072)...
	2019-12-19 19:17:03 Stop child process func_1 (5360)...
	2019-12-19 19:17:07 hello world!
	2019-12-19 19:17:07 Stop child process func_2 (19072)...
	2019-12-19 19:17:07 Process end .

结果显示

	主进程的 Process end .是在func1 和func2 结束之后才打印出来的。

一定要注意上面的方法中 start 和 join 方法，具体的我们可以看一下我的博文，关于 join 的一些简单的介绍

[python | 多线程间的运行](https://benpaodewoniu.github.io/2019/12/19/multiprocess2/)

直接上一下代码

```python
from multiprocessing import Process
import os
import time

def now():
    return str(time.strftime('%Y-%m-%d %H:%M:%S', time.localtime()))


def func_1(name):
    print(now() + ' Run child process %s (%s)...' % (name, os.getpid()))
    time.sleep(4)
    print(now() + ' Stop child process %s (%s)...\n' % (name, os.getpid()))


def func_2(name):
    print(now() + ' Run child process %s (%s)...' % (name, os.getpid()))
    time.sleep(8)
    print(now() + ' hello world!')
    print(now() + ' Stop child process %s (%s)...\n' % (name, os.getpid()))


if __name__ == '__main__':
    print ('Parent process %s.' % os.getpid())
    p1 = Process(target=func_1, args=('func_1',))
    p2 = Process(target=func_2, args=('func_2',))
    print (now() + ' Process start.')
    p1.start()
    p1.join()
    p2.start()
    p2.join()
    print (now() + ' Process end .')
```

其输出如下

	Parent process 22100.
	2019-12-19 19:25:20 Process start.
	2019-12-19 19:25:20 Run child process func_1 (20032)...
	2019-12-19 19:25:24 Stop child process func_1 (20032)...
	2019-12-19 19:25:24 Run child process func_2 (17044)...
	2019-12-19 19:25:32 hello world!
	2019-12-19 19:25:32 Stop child process func_2 (17044)...
	2019-12-19 19:25:32 Process end .

可以看出其是阻塞进程的

## 去掉 join() 函数

```python
if __name__ == '__main__':
    print ('Parent process %s.' % os.getpid())
    p1 = Process(target=func_1, args=('func_1',))
    p2 = Process(target=func_2, args=('func_2',))
    print now() + ' Process start.'
    p1.start()
    p2.start()
    print now() + ' Process end .'
```

结果显示

	Parent process 20188.
	2019-12-19 19:19:59 Process start.
	2019-12-19 19:19:59 Process end .
	2019-12-19 19:19:59 Run child process func_2 (22872)...
	2019-12-19 19:19:59 Run child process func_1 (17924)...
	2019-12-19 19:20:03 Stop child process func_1 (17924)...
	2019-12-19 19:20:07 hello world!
	2019-12-19 19:20:07 Stop child process func_2 (22872)...

结果显示主线程 "Process end" 紧跟着 "Process start",然后是 func_1 func_2 的动作。而不是等func_1 func_2执行完才执行。

## 去掉func_2 的 join()

```python
if __name__ == '__main__':
    print ('Parent process %s.' % os.getpid())
    p1 = Process(target=func_1, args=('func_1',))
    p2 = Process(target=func_2, args=('func_2',))
    print now() + ' Process start.'
    p1.start()
    p2.start()
    p1.join()  ### 在p1 执行完之后 。不等待p2 执行，主进程结束。
    print now() + ' Process end .'
```

结果如下:

	Parent process 13360.
	2019-12-19 19:21:35 Process start.
	2019-12-19 19:21:35 Run child process func_1 (20812)...
	2019-12-19 19:21:35 Run child process func_2 (22448)...
	2019-12-19 19:21:39 Stop child process func_1 (20812)...
	2019-12-19 19:21:39 Process end .
	2019-12-19 19:21:43 hello world!
	2019-12-19 19:21:43 Stop child process func_2 (22448)...

结果显示主线程 "Process end"在func_1 执行结束之后输出而没有等待func_2 执行完毕。

利用多线程时，一般都先让子线程调用start() ，然后再去调用join()，让主进程等待子进程结束才继续走后续的逻辑。