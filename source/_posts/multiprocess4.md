---
title: Python | 多线程同步锁，死锁和递归锁
date: 2019-12-22 20:37:01
categories:
- multiprocess
tags:
- multiprocess
- 多进程
- 多线程
---
如题。

<!-- more -->

# Python同步锁

锁通常被用来实现对共享资源的同步访问。为每一个共享资源创建一个Lock对象，当你需要访问该资源时，调用acquire方法来获取锁对象（如果其它线程已经获得了该锁，则当前线程需等待其被释放），待资源访问完后，再调用release方法释放锁。

下面我们来举个例子说明如果多线程在没有同步锁的情况下访问公共资源会导致什么情况

{% codeblock %}
import threading
import time

num = 100

def fun_sub():
    global num
    # num -= 1
    num2 = num
    time.sleep(0.001)
    num = num2-1

if __name__ == '__main__':
    print('开始测试同步锁 at %s' % time.ctime())

    thread_list = []
    for thread in range(100):
        t = threading.Thread(target=fun_sub)
        t.start()
        thread_list.append(t)

    for t in thread_list:
        t.join()
    print('num is %d' % num)
    print('结束测试同步锁 at %s' % time.ctime())
{% endcodeblock %}

上面的例子其实很简单就是创建100的线程，然后每个线程去从公共资源num变量去执行减1操作，按照正常情况下面，等到代码执行结束，打印num变量，应该得到的是0，因为100个线程都去执行了一次减1的操作。

但是结果却不是我们想想的，我们看看结果

	开始测试同步锁 at Sun Apr 28 09:56:45 2019
	num is 91
	结束测试同步锁 at Sun Apr 28 09:56:45 2019

我们会发现，每次执行的结果num值都不是一样的，上面显示的是91，那就存在问题了，为什么结果不是0呢？

我们来看看上面代码的执行流程。

	1.因为GIL，只有一个线程（假设线程1）拿到了num这个资源，然后把变量赋值给num2,sleep 0.001秒，这时候num=100

	2.当第一个线程sleep 0.001秒这个期间，这个线程会做yield操作，就是把cpu切换给别的线程执行（假设线程2拿到个GIL，获得cpu使用权），线程2也和线程1一样也拿到num,返回赋值给num2，然sleep,这时候，其实num还是=100.

	3.线程2 sleep时候，又要yield操作，假设线程3拿到num,执行上面的操作，其实num有可能还是100

	4.等到后面cpu重新切换给线程1，线程2，线程3上执行的时候，他们执行减1操作后，其实等到的num其实都是99，而不是顺序递减的。

	5.其他剩余的线程操作如上

大家应该发现问题了，结果和我们想想的不一样，那我们怎么才能等到我们想要的结果呢？就是100个线程操作num变量得到最后结果为0？

这里就要借助于python的同步锁了，也就是同一时间只能放一个线程来操作num变量，减1之后，后面的线程操作来操作num变量。看看下面我们怎么实现。

{% codeblock %}
import threading
import time

num = 100

def fun_sub():
    global num
    lock.acquire()
    print('----加锁----')
    print('现在操作共享资源的线程名字是:',t.name)
    num2 = num
    time.sleep(0.001)
    num = num2-1
    lock.release()
    print('----释放锁----')

if __name__ == '__main__':
    print('开始测试同步锁 at %s' % time.ctime())

    lock = threading.Lock() #创建一把同步锁

    thread_list = []
    for thread in range(100):
        t = threading.Thread(target=fun_sub)
        t.start()
        thread_list.append(t)

    for t in thread_list:
        t.join()
    print('num is %d' % num)
    print('结束测试同步锁 at %s' % time.ctime())
{% endcodeblock %}

看到上面我们给中间的减1代码块，加个一把同步锁，这样，我们就可以得到我们想要的结果了，这就是同步锁的作用，一次只有一个线程操作同享资源。

看看上面代码执行的结果：

	.......
	----加锁----
	现在操作共享资源的线程名字是: Thread-98
	----释放锁----
	----加锁----
	现在操作共享资源的线程名字是: Thread-100
	----释放锁----
	num is 0
	结束测试同步锁 at Sun Apr 28 12:08:27 2019

# Python死锁

死锁的这个概念在很多地方都存在，比较在数据中，大概介绍下私有是怎么产生的

	1.A拿了一个苹果
	2.B拿了一个香蕉
	3.A现在想再拿个香蕉，就在等待B释放这个香蕉
	4.B同时想要再拿个苹果，这时候就等待A释放苹果
	5.这样就是陷入了僵局，这就是生活中的死锁

python中在线程间共享多个资源的时候，如果两个线程分别占有一部分资源并且同时等待对方的资源，就会造成死锁，因为系统判断这部分资源都正在使用，所有这两个线程在无外力作用下将一直等待下去。下面是一个死锁的例子：

{% codeblock %}
import threading
import time

lock_apple = threading.Lock()
lock_banana = threading.Lock()

class MyThread(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        self.fun1()
        self.fun2()

    def fun1(self):

        lock_apple.acquire()  # 如果锁被占用,则阻塞在这里,等待锁的释放

        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果",time.ctime()))

        lock_banana.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉",time.ctime()))
        lock_banana.release()
        lock_apple.release()


    def fun2(self):

        lock_banana.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉",time.ctime()))
        time.sleep(0.1)

        lock_apple.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果",time.ctime()))
        lock_apple.release()

        lock_banana.release()

if __name__ == "__main__":
    for i in range(0, 10):  #建立10个线程
        my_thread = MyThread()  #类继承法是python多线程的另外一种实现方式
        my_thread.start()
{% endcodeblock %}

代码执行hung住，死锁了

	线程 Thread-1 , 想拿: 苹果--Sun Apr 28 12:21:06 2019
	线程 Thread-1 , 想拿: 香蕉--Sun Apr 28 12:21:06 2019
	线程 Thread-1 , 想拿: 香蕉--Sun Apr 28 12:21:06 2019
	线程 Thread-2 , 想拿: 苹果--Sun Apr 28 12:21:06 2019
	Process finished with exit code -1

上面的代码其实就是描述了苹果和香蕉的故事。大家可以仔细看看过程。下面我们看看执行流程

	1.fun1中，线程1先拿了苹果，然后拿了香蕉，然后释放香蕉和苹果，然后再在fun2中又拿了香蕉，sleep 0.1秒。
	2.在线程1的执行过程中，线程2进入了，因为苹果被线程1释放了，线程2这时候获得了苹果，然后想拿香蕉
	3.这时候就出现问题了，线程一拿完香蕉之后想拿苹果，返现苹果被线程2拿到了，线程2拿到苹果执行，想拿香蕉，发现香蕉被线程1持有了
	4.双向等待，出现死锁，代码执行不下去了

上面就是大概的执行流程和死锁出现的原因。在这种情况下就是在同一线程中多次请求同一资源时候出现的问题。

# Python递归锁RLock

为了支持在同一线程中多次请求同一资源，python提供了"递归锁"：threading.RLock。RLock内部维护着一个Lock和一个counter变量，counter记录了acquire的次数，从而使得资源可以被多次acquire。直到一个线程所有的acquire都被release，其他的线程才能获得资源。

下面我们用递归锁RLock解决上面的死锁问题

{% codeblock %}
import threading
import time

lock = threading.RLock()  #递归锁


class MyThread(threading.Thread):

    def __init__(self):
        threading.Thread.__init__(self)

    def run(self):
        self.fun1()
        self.fun2()

    def fun1(self):

        lock.acquire()  # 如果锁被占用,则阻塞在这里,等待锁的释放

        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果",time.ctime()))

        lock.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉",time.ctime()))
        lock.release()
        lock.release()


    def fun2(self):

        lock.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "香蕉",time.ctime()))
        time.sleep(0.1)

        lock.acquire()
        print ("线程 %s , 想拿: %s--%s" %(self.name, "苹果",time.ctime()))
        lock.release()

        lock.release()

if __name__ == "__main__":
    for i in range(0, 10):  #建立10个线程
        my_thread = MyThread()  #类继承法是python多线程的另外一种实现方式
        my_thread.start()
{% endcodeblock %}

上面我们用一把递归锁，就解决了多个同步锁导致的死锁问题。大家可以把RLock理解为大锁中还有小锁，只有等到内部所有的小锁，都没有了，其他的线程才能进入这个公共资源。

另外一点前面没有就算用类继承的方法实现python多线程，这个大家可以查下，就算继承Thread类，然后重新run方法来实现。

最后大家可能还有个疑问，就算如果我们都加锁了，也就是单线程了，那我们还要开多线程有什么用呢？这里解释下，在访问共享资源的时候，锁是一定要存在了，但是我们的代码中不是总是在访问公共资源的，还有一些其他的逻辑可以使用多线程，所以我们在代码里面加锁的时候，要注意在什么地方加，对性能的影响最小，这个就靠对逻辑的理解了。