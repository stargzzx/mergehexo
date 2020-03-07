---
title: Python多进程进阶
date: 2019-12-22 21:17:54
categories:
- multiprocess
tags:
- multiprocess
- 多进程
- 多线程
---
今天开始会开启python多进程的内容，大家看过前面文章的应该都知道python中的GIL的存在，也就是多线程的时候，同一时间只能有一个线程在CPU上运行，而且是单个CPU上运行，不管你的CPU有多少核数。如果想要充分地使用多核CPU的资源，在python中大部分情况需要使用多进程。

<!-- more -->

<br/>

# Python多进程模块

<br/>

Python中的多进程是通过multiprocessing包来实现的，和多线程的threading.Thread差不多，它可以利用multiprocessing.Process对象来创建一个进程对象。这个进程对象的方法和线程对象的方法差不多也有start(), run(), join()等方法，其中有一个方法不同Thread线程对象中的守护线程方法是setDeamon，而Process进程对象的守护进程是通过设置daemon属性来完成的。

下面说说Python多进程的实现方法，和多线程类似

<br/>

# Python多进程实现方法一

<br/>

{% codeblock %}
from multiprocessing import  Process

def fun1(name):
    print('测试%s多进程' %name)

if __name__ == '__main__':
    process_list = []
    for i in range(5):  #开启5个子进程执行fun1函数
        p = Process(target=fun1,args=('Python',)) #实例化进程对象
        p.start()
        process_list.append(p)

    for i in process_list:
        p.join()

    print('结束测试')
{% endcodeblock %}

结果

	测试Python多进程
	测试Python多进程
	测试Python多进程
	测试Python多进程
	测试Python多进程
	结束测试

上面的代码开启了5个子进程去执行函数，我们可以观察结果，是同时打印的，这里实现了真正的并行操作，就是多个CPU同时执行任务。我们知道进程是python中最小的资源分配单元，也就是进程中间的数据，内存是不共享的，每启动一个进程，都要独立分配资源和拷贝访问的数据，所以进程的启动和销毁的代价是比较大了，所以在实际中使用多进程，要根据服务器的配置来设定。

<br/>

# Python多进程实现方法二

<br/>

还记得python多线程的第二种实现方法吗?是通过类继承的方法来实现的，python多进程的第二种实现方式也是一样的

{% codeblock %}
from multiprocessing import  Process

class MyProcess(Process): #继承Process类
    def __init__(self,name):
        super(MyProcess,self).__init__()
        self.name = name

    def run(self):
        print('测试%s多进程' % self.name)


if __name__ == '__main__':
    process_list = []
    for i in range(5):  #开启5个子进程执行fun1函数
        p = MyProcess('Python') #实例化进程对象
        p.start()
        process_list.append(p)

    for i in process_list:
        p.join()

    print('结束测试')
{% endcodeblock %}

结果

	测试Python多进程
	测试Python多进程
	测试Python多进程
	测试Python多进程
	测试Python多进程
	结束测试

效果和第一种方式一样。

我们可以看到Python多进程的实现方式和多线程的实现方式几乎一样。

<br/>

# Process类的其他方法

<br/>

构造方法：

	Process([group [, target [, name [, args [, kwargs]]]]])
	　　group: 线程组 
	　　target: 要执行的方法
	　　name: 进程名
	　　args/kwargs: 要传入方法的参数

	实例方法：
	　　is_alive()：返回进程是否在运行,bool类型。
	　　join([timeout])：阻塞当前上下文环境的进程程，直到调用此方法的进程终止或到达指定的timeout（可选参数）。
	　　start()：进程准备就绪，等待CPU调度
	　　run()：strat()调用run方法，如果实例进程时未制定传入target，这star执行t默认run()方法。
	　　terminate()：不管任务是否完成，立即停止工作进程

	属性：
	　　daemon：和线程的setDeamon功能一样
	　　name：进程名字
	　　pid：进程号

关于join，daemon的使用和python多线程一样，这里就不在复述了，大家可以看看以前的python多线程系列文章。

<br/>

# Python多线程的通信

<br/>

进程是系统独立调度核分配系统资源（CPU、内存）的基本单位，进程之间是相互独立的，每启动一个新的进程相当于把数据进行了一次克隆，子进程里的数据修改无法影响到主进程中的数据，不同子进程之间的数据也不能共享，这是多进程在使用中与多线程最明显的区别。但是难道Python多进程中间难道就是孤立的吗？当然不是，python也提供了多种方法实现了多进程中间的通信和数据共享（可以修改一份数据）

## 进程对列Queue

Queue在多线程中也说到过，在生成者消费者模式中使用，是线程安全的，是生产者和消费者中间的数据管道，那在python多进程中，它其实就是进程之间的数据管道，实现进程通信。

{% codeblock %}
from multiprocessing import Process,Queue


def fun1(q,i):
    print('子进程%s 开始put数据' %i)
    q.put('我是%s 通过Queue通信' %i)

if __name__ == '__main__':
    q = Queue()

    process_list = []
    for i in range(3):
        p = Process(target=fun1,args=(q,i,))  #注意args里面要把q对象传给我们要执行的方法，这样子进程才能和主进程用Queue来通信
        p.start()
        process_list.append(p)

    for i in process_list:
        p.join()

    print('主进程获取Queue数据')
    print(q.get())
    print(q.get())
    print(q.get())
    print('结束测试')
{% endcodeblock %}

结果

	子进程0 开始put数据
	子进程1 开始put数据
	子进程2 开始put数据
	主进程获取Queue数据
	我是0 通过Queue通信
	我是1 通过Queue通信
	我是2 通过Queue通信
	结束测试

上面的代码结果可以看到我们主进程中可以通过Queue获取子进程中put的数据，实现进程间的通信。

## 管道Pipe

管道Pipe和Queue的作用大致差不多，也是实现进程间的通信，下面之间看怎么使用吧

{% codeblock %}
from multiprocessing import Process, Pipe
def fun1(conn):
    print('子进程发送消息：')
    conn.send('你好主进程')
    print('子进程接受消息：')
    print(conn.recv())
    conn.close()

if __name__ == '__main__':
    conn1, conn2 = Pipe() #关键点，pipe实例化生成一个双向管
    p = Process(target=fun1, args=(conn2,)) #conn2传给子进程
    p.start()
    print('主进程接受消息：')
    print(conn1.recv())
    print('主进程发送消息：')
    conn1.send("你好子进程")
    p.join()
    print('结束测试')
{% endcodeblock %}

结果

	主进程接受消息：
	子进程发送消息：
	子进程接受消息：
	你好主进程
	主进程发送消息：
	你好子进程
	结束测试

上面可以看到主进程和子进程可以相互发送消息

## Managers

Queue和Pipe只是实现了数据交互，并没实现数据共享，即一个进程去更改另一个进程的数据。那么久要用到Managers

{% codeblock %}
from multiprocessing import Process, Manager

def fun1(dic,lis,index):

    dic[index] = 'a'
    dic['2'] = 'b'    
    lis.append(index)    #[0,1,2,3,4,0,1,2,3,4,5,6,7,8,9]
    #print(l)

if __name__ == '__main__':
    with Manager() as manager:
        dic = manager.dict()#注意字典的声明方式，不能直接通过{}来定义
        l = manager.list(range(5))#[0,1,2,3,4]

        process_list = []
        for i in range(10):
            p = Process(target=fun1, args=(dic,l,i))
            p.start()
            process_list.append(p)

        for res in process_list:
            res.join()
        print(dic)
        print(l)
{% endcodeblock %}

结果：

	{0: 'a', '2': 'b', 3: 'a', 1: 'a', 2: 'a', 4: 'a', 5: 'a', 7: 'a', 6: 'a', 8: 'a', 9: 'a'}
	[0, 1, 2, 3, 4, 0, 3, 1, 2, 4, 5, 7, 6, 8, 9]

可以看到主进程定义了一个字典和一个列表，在子进程中，可以添加和修改字典的内容，在列表中插入新的数据，实现进程间的数据共享，即可以共同修改同一份数据

<br/>

# 进程池

<br/>

进程池内部维护一个进程序列，当使用时，则去进程池中获取一个进程，如果进程池序列中没有可供使用的进进程，那么程序就会等待，直到进程池中有可用进程为止。就是固定有几个进程可以使用。

进程池中有两个方法：

apply：同步，一般不使用

apply_async：异步

{% codeblock %}
from  multiprocessing import Process,Pool
import os, time, random

def fun1(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    pool = Pool(5) #创建一个5个进程的进程池

    for i in range(10):
        pool.apply_async(func=fun1, args=(i,))

    pool.close()
    pool.join()
    print('结束测试')
{% endcodeblock %}

结果

	Run task 0 (37476)...
	Run task 1 (4044)...
	Task 0 runs 0.03 seconds.
	Run task 2 (37476)...
	Run task 3 (17252)...
	Run task 4 (16448)...
	Run task 5 (24804)...
	Task 2 runs 0.27 seconds.
	Run task 6 (37476)...
	Task 1 runs 0.58 seconds.
	Run task 7 (4044)...
	Task 3 runs 0.98 seconds.
	Run task 8 (17252)...
	Task 5 runs 1.13 seconds.
	Run task 9 (24804)...
	Task 6 runs 1.46 seconds.
	Task 4 runs 2.73 seconds.
	Task 8 runs 2.18 seconds.
	Task 7 runs 2.93 seconds.
	Task 9 runs 2.93 seconds.
	结束测试

对Pool对象调用join()方法会等待所有子进程执行完毕，调用join()之前必须先调用close()，调用close()之后就不能继续添加新的Process了。

## 进程池map方法

因为网上看到这个例子觉得不错，所以这里就不自己写案例，这个案例比较有说服力

{% codeblock %}
import os 
import PIL 

from multiprocessing import Pool 
from PIL import Image

SIZE = (75,75)
SAVE_DIRECTORY = \'thumbs\'

def get_image_paths(folder):
    return (os.path.join(folder, f) 
            for f in os.listdir(folder) 
            if \'jpeg\' in f)

def create_thumbnail(filename): 
    im = Image.open(filename)
    im.thumbnail(SIZE, Image.ANTIALIAS)
    base, fname = os.path.split(filename) 
    save_path = os.path.join(base, SAVE_DIRECTORY, fname)
    im.save(save_path)

if __name__ == \'__main__\':
    folder = os.path.abspath(
        \'11_18_2013_R000_IQM_Big_Sur_Mon__e10d1958e7b766c3e840\')
    os.mkdir(os.path.join(folder, SAVE_DIRECTORY))

    images = get_image_paths(folder)

    pool = Pool()
    pool.map(creat_thumbnail, images) #关键点，images是一个可迭代对象
    pool.close()
    pool.join()
{% endcodeblock %}

上边这段代码的主要工作就是将遍历传入的文件夹中的图片文件，一一生成缩略图，并将这些缩略图保存到特定文件夹中。这我的机器上，用这一程序处理 6000 张图片需要花费 27.9 秒。 map 函数并不支持手动线程管理，反而使得相关的 debug 工作也变得异常简单。

map在爬虫的领域里也可以使用，比如多个URL的内容爬取，可以把URL放入元祖里，然后传给执行函数。