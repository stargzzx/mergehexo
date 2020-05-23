---
title: python | 实现和启动多进程的几种方法和优劣
date: 2020-05-23 17:20:45
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
实现多进程一共有多个方法。

- fork()
- 进程类
- 继承进程类

<!-- more -->

<br/>

# 实现进程的方法

<br/>

## fork()

要知道，每个 Python 程序在执行时，系统都会生成一个新的进程，该进程又称父进程（或主进程）。在此基础上，Python os 模块还提供有 fork() 函数，该函数可以在当前程序中再创建出一个进程（又称子进程）。

也就是说，程序中通过引入 os 模块，并调用其提供的 fork() 函数，程序中会拥有 2 个进程，其中父进程负责执行整个程序代码，而通过 fork() 函数创建出的子进程，会从创建位置开始，执行后续所有的程序（包含创建子进程的代码）。

>注意，os.fork() 函数在 Windows 系统上无效，只在 UNIX 及类 UNIX 系统上（包括UNIX、Linux 和 Mac OS X）效。

fork() 方法的语法格式为：

>pid = os.fork()

其中，pid 作为函数的返回值，主进程和子进程都会执行该语句，但主进程执行 fork() 函数得到的 pid 值为非 0 值（其实是子进程的进程 ID），而子进程执行该语句得到的 pid 值为 0。因此，pid 常常作为区分父进程和子进程的标志。

>在大多数操作系统中，都会为执行的进程配备唯一的 ID 号，os 模块提供了 getpid() 和 getppid() 函数，可分别用来获取当前进程的 ID 号和父进程的 ID 号。

下面程序演示了 os.fork() 方法的具体使用：

```python
import os
print('父进程 ID =', os.getpid())
# 创建一个子进程，下面代码会被两个进程执行
pid = os.fork()
print('当前进程 ID =',os.getpid()," pid=",pid)
#根据 pid 值，分别为子进程和父进程布置任务
if pid == 0:
    print('子进程, ID=',os.getpid()," 父进程 ID=",os.getppid())
else:
    print('父进程, ID=',os.getpid()," pid=",pid)
```

程序输出结果为：
    
    父进程 ID = 2884
    当前进程 ID = 2884 pid= 2885
    父进程, ID= 2884 pid= 2885
    当前进程 ID = 2885 pid= 0
    子进程, ID= 2885 父进程 ID= 2884

从输出结果可以看到，当前程序在执行时，系统生成了进程号为 2884 的进程，该进程负责执行当前程序中的所有代码。与此同时，程序第 4 行创建了进程号为 2885 的子进程，该进程将执行第 4 行开始（包括第 4 行）后续的所有代码。

注意，程序第 7 行代码的 if 判断语句，通过判断 pid 值是否为 0，分别为父进程和 fork() 函数创建的子进程布置了不同的执行任务，即子进程负责执行 if 代码块，而父进程则负责执行 else 代码块。

前面介绍了使用 os.fork() 函数实现多进程编程，该方法最明显的缺陷就是不适用于 Windows 系统。

## 多进程类

Python multiprocessing 模块提供了 Process 类，该类可用来在 Windows 平台上创建新进程。和使用 Thread 类创建多线程方法类似，使用 Process 类创建多进程也有以下 2 种方式：

- 直接创建 Process 类的实例对象，由此就可以创建一个新的进程；
- 通过继承 Process 类的子类，创建实例对象，也可以创建新的进程。注意，继承 Process 类的子类需重写父类的 run() 方法。

Python Process类常用属性和方法

<br/>

|属性名或方法名|	功能|
|---|---|
|run()|	第 2 种创建进程的方式需要用到，继承类中需要对方法进行重写，该方法中包含的是新进程要执行的代码。|
|start()|	和启动子线程一样，新创建的进程也需要手动启动，该方法的功能就是启动新创建的线程。|
|join([timeout])|	和 thread 类 join() 方法的用法类似，其功能是在多进程执行过程，其他进程必须等到调用 join() 方法的进程执行完毕（或者执行规定的 timeout 时间）后，才能继续执行；|
|is_alive()|	判断当前进程是否还活着。|
|terminate()|	中断该进程。|
|name属性|	可以为该进程重命名，也可以获得该进程的名称。|
|daemon|	和守护线程类似，通过设置该属性为 True，可将新建进程设置为“守护进程”。|
|pid|	返回进程的 ID 号。大多数操作系统都会为每个进程配备唯一的 ID 号。|

### 通过Process类创建进程

和使用 thread 类创建子线程的方式非常类似，使用 Process 类创建实例化对象，其本质是调用该类的构造方法创建新进程。Process 类的构造方法格式如下：

    def __init__(self,group=None,target=None,name=None,args=(),kwargs={})

其中，各个参数的含义为：

- group：该参数未进行实现，不需要传参；
- target：为新建进程指定执行任务，也就是指定一个函数；
- name：为新建进程设置名称；
- args：为 target 参数指定的参数传递非关键字参数；
- kwargs：为 target 参数指定的参数传递关键字参数。

下面程序演示了如何用 Process 类创建新进程。

```python
from multiprocessing import Process
import os
print("当前进程ID：",os.getpid())
# 定义一个函数，准备作为新进程的 target 参数
def action(name,*add):
    print(name)
    for arc in add:
        print("%s --当前进程%d" % (arc,os.getpid()))
if __name__=='__main__':
    #定义为进程方法传入的参数
    my_tuple = ("http://c.biancheng.net/python/",\
                "http://c.biancheng.net/shell/",\
                "http://c.biancheng.net/java/")
    #创建子进程，执行 action() 函数
    my_process = Process(target = action, args = ("my_process进程",*my_tuple))
    #启动子进程
    my_process.start()
    #主进程执行该函数
    action("主进程",*my_tuple)
```

程序执行结果为：

    当前进程ID： 12980
    主进程
    http://c.biancheng.net/python/ --当前进程12980
    http://c.biancheng.net/shell/ --当前进程12980
    http://c.biancheng.net/java/ --当前进程12980
    当前进程ID： 12860
    my_process进程
    http://c.biancheng.net/python/ --当前进程12860
    http://c.biancheng.net/shell/ --当前进程12860
    http://c.biancheng.net/java/ --当前进程12860

需要说明的是，通过 multiprocessing.Process 来创建并启动进程时，程序必须先判断 `if __name__=='__main__':`，否则运行该程序会引发异常。

此程序中有 2 个进程，分别为主进程和我们创建的新进程，主进程会执行整个程序，而子进程不会执行 `if __name__ == '__main__'` 中包含的程序，而是先执行此判断语句之外的所有可执行程序，然后再执行我们分配让它的任务（也就是通过 target 参数指定的函数）。

### 通过Process继承类创建进程

和使用 thread 子类创建线程的方式类似，除了直接使用 Process 类创建进程，还可以通过创建 Process 的子类来创建进程。

需要注意的是，在创建 Process 的子类时，需在子类内容重写 run() 方法。实际上，该方法所起到的作用，就如同第一种创建方式中 target 参数执行的函数。

另外，通过 Process 子类创建进程，和使用 Process 类一样，先创建该类的实例对象，然后调用 start() 方法启动该进程。下面程序演示如何通过 Process 子类创建一个进程。

```python
from multiprocessing import Process
import os
print("当前进程ID：",os.getpid())
# 定义一个函数，供主进程调用
def action(name,*add):
    print(name)
    for arc in add:
        print("%s --当前进程%d" % (arc,os.getpid()))
#自定义一个进程类
class My_Process(Process):
    def __init__(self,name,*add):
        super().__init__()
        self.name = name
        self.add = add
    def run(self):
        print(self.name)
        for arc in self.add:
            print("%s --当前进程%d" % (arc,os.getpid()))
if __name__=='__main__':
    #定义为进程方法传入的参数
    my_tuple = ("http://c.biancheng.net/python/",\
                "http://c.biancheng.net/shell/",\
                "http://c.biancheng.net/java/")
    my_process = My_Process("my_process进程",*my_tuple)
    #启动子进程
    my_process.start()
    #主进程执行该函数
    action("主进程",*my_tuple)
```

程序执行结果为：
    
    当前进程ID： 22240
    主进程
    http://c.biancheng.net/python/ --当前进程22240
    http://c.biancheng.net/shell/ --当前进程22240
    http://c.biancheng.net/java/ --当前进程22240
    当前进程ID： 18848
    my_process进程
    http://c.biancheng.net/python/ --当前进程18848
    http://c.biancheng.net/shell/ --当前进程18848
    http://c.biancheng.net/java/ --当前进程18848

显然，该程序的运行结果与上一个程序的运行结果大致相同，它们只是创建进程的方式略有不同而已。

>推荐使用第一种方式来创建进程，因为这种方式不仅编程简单，而且进程直接包装 target 函数，具有更清晰的逻辑结构。

<br/>

# 进程启动的2种方式

<br/>

- 使用 os.fork() 函数创建的子进程，会从创建位置处开始，执行后续所有的程序，主进程如何执行，则子进程就如何执行；
- 而使用 Process 类创建的进程，则仅会执行`if "__name__"="__main__"`之外的可执行代码以及该类构造方法中 target 参数指定的函数（使用 Process 子类创建的进程，只能执行重写的 run() 方法）。

实际上，Python 创建的子进程执行的内容，和启动该进程的方式有关。而根据不同的平台，启动进程的方式大致可分为以下 3 种：

- spawn
    - 使用此方式启动的进程，只会执行和 target 参数或者 run() 方法相关的代码。Windows 平台只能使用此方法，事实上该平台默认使用的也是该启动方式。相比其他两种方式，此方式启动进程的效率最低。
- fork
    - 使用此方式启动的进程，基本等同于主进程（即主进程拥有的资源，该子进程全都有）。因此，该子进程会从创建位置起，和主进程一样执行程序中的代码。注意，此启动方式仅适用于 UNIX 平台，os.fork() 创建的进程就是采用此方式启动的。
- forserver
    - 使用此方式，程序将会启动一个服务器进程。即当程序每次请求启动新进程时，父进程都会连接到该服务器进程，请求由服务器进程来创建新进程。通过这种方式启动的进程不需要从父进程继承资源。注意，此启动方式只在 UNIX 平台上有效。

>总的来说，使用类 UNIX 平台，启动进程的方式有以上 3 种，而使用 Windows 平台，只能选用 spawn 方式（默认即可）。

在了解以上 3 种进程启动方式的基础上，我们还需要知道手动设置进程启动方式的方法，大致有以下  2 种。

## set_start_method()

Python multiprocessing 模块提供了一个 `set_start_method()` 函数，该函数可用于设置启动进程的方式。需要注意的是，该函数的调用位置，必须位于所有与多进程有关的代码之前。

例如，下面代码演示了如何显式设置进程的启动方式：

```python
import multiprocessing
import os
print("当前进程ID：",os.getpid())
# 定义一个函数，准备作为新进程的 target 参数
def action(name,*add):
    print(name)
    for arc in add:
        print("%s --当前进程%d" % (arc,os.getpid()))
if __name__=='__main__':
    #定义为进程方法传入的参数
    my_tuple = ("http://c.biancheng.net/python/",\
                "http://c.biancheng.net/shell/",\
                "http://c.biancheng.net/java/")
    #设置进程启动方式
    multiprocessing.set_start_method('spawn')
   
    #创建子进程，执行 action() 函数
    my_process = multiprocessing.Process(target = action, args = ("my_process进程",*my_tuple))
    #启动子进程
    my_process.start()
```

程序执行结果为：
    
    当前进程ID： 24500
    当前进程ID： 17300
    my_process进程
    http://c.biancheng.net/python/ --当前进程17300
    http://c.biancheng.net/shell/ --当前进程17300
    http://c.biancheng.net/java/ --当前进程17300

注意，由于此程序中进程的启动方式为 spawn，因此该程序可以在任意（ Windows 和类 UNIX 上都可以 ）平台上执行。

## get_context()

还可以使用 multiprocessing 模块提供的 `get_context()` 函数来设置进程启动的方法，调用该函数时可传入 

- spawn
- fork
- forkserver

作为参数，用来指定进程启动的方式。

需要注意的一点是，前面在创建进程是，使用的 multiprocessing.Process() 这种形式，而在使用 get_context() 函数设置启动进程方式时，需用该函数的返回值，代替 multiprocessing 模块调用 Process()。 

例如，下面程序演示了如何使用 get_context() 函数设置进程启动：

```python
import multiprocessing
import os
print("当前进程ID：",os.getpid())
# 定义一个函数，准备作为新进程的 target 参数
def action(name,*add):
    print(name)
    for arc in add:
        print("%s --当前进程%d" % (arc,os.getpid()))
if __name__=='__main__':
    #定义为进程方法传入的参数
    my_tuple = ("http://c.biancheng.net/python/",\
                "http://c.biancheng.net/shell/",\
                "http://c.biancheng.net/java/")
    #设置使用 fork 方式启动进程
    ctx = multiprocessing.get_context('spawn')
   
    #用 ctx 代替 multiprocessing 模块创建子进程，执行 action() 函数
    my_process = ctx.Process(target = action, args = ("my_process进程",*my_tuple))
    #启动子进程
    my_process.start()
```
程序执行结果为：
    
    当前进程ID： 18632
    当前进程ID： 16700
    my_process进程
    http://c.biancheng.net/python/ --当前进程16700
    http://c.biancheng.net/shell/ --当前进程16700
    http://c.biancheng.net/java/ --当前进程16700

以上仅演示了在 Windows 平台上设置进程启动方式的效果，有兴趣的可自行尝试选择类 UNIX 平台测试其他启动进程的方式。
