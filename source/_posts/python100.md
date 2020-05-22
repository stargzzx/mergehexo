---
title: python | Python互斥锁（Lock）：解决多线程安全问题
date: 2018-12-20 09:44:42
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
Lock 解决线程安全性问题。

<!-- more -->

<br/>

# 为什么要有锁

<br/>

在我下面的博文中，给出了这样一个例子.

- [python | GLI锁机制](https://benpaodewoniu.github.io/2018/12/19/multiprocess3/)

>注意，有了 GIL，并不意味着 Python 程序员就不用去考虑线程安全了，因为即便 GIL 仅允许一个 Python 线程执行，但别忘了 Python 还有 check interval 这样的抢占机制。

<br/>

# 什么是锁？

<br/>

多线程的优势在于并发性，即可以同时运行多个任务。但是当线程需要使用共享数据时，也可能会由于数据不同步产生“错误情况”，这是由系统的线程调度具有一定的随机性造成的。

互斥锁的作用就是解决数据不同步问题。关于互斥锁，有一个经典的“银行取钱”问题。银行取钱的基本流程可以分为如下几个步骤：

1. 用户输入账户、密码，系统判断用户的账户、密码是否匹配。
2. 用户输入取款金额。
3. 系统判断账户余额是否大于取款金额。
4. 如果余额大于取款金额，则取款成功；如果余额小于取款金额，则取款失败。

乍一看上去，这确实就是日常生活中的取款流程，这个流程没有任何问题。但一旦将这个流程放在多线程并发的场景下，就有可能出现问题。注意，此处说的是有可能，并不是一定。也许你的程序运行了一百万次都没有出现问题，但没有出现问题并不等于没有问题！

按照上面的流程编写取款程序，并使用两个线程分别模拟两个人使用同一个账户做并发取钱操作。此处忽略检查账户和密码的操作，仅仅模拟后面三步操作。下面先定义一个账户类，该账户类封装了账户编号和余额两个成员变量。

```python
class Account:
    # 定义构造器
    def __init__(self, account_no, balance):
        # 封装账户编号、账户余额的两个成员变量
        self.account_no = account_no
        self.balance = balance
```

接下来程序会定义一个模拟取钱的函数，该函数根据执行账户、取钱数量进行取钱操作，取钱的逻辑是当账户余额不足时无法提取现金，当余额足够时系统吐出钞票，余额减少。

程序的主程序非常简单，仅仅是创建一个账户，并启动两个线程从该账户中取钱。程序如下：

```python
import threading
import time
import Account
# 定义一个函数来模拟取钱操作
def draw(account, draw_amount):
    # 账户余额大于取钱数目
    if account.balance >= draw_amount:
        # 吐出钞票
        print(threading.current_thread().name\
            + "取钱成功！吐出钞票:" + str(draw_amount))
#        time.sleep(0.001)
        # 修改余额
        account.balance -= draw_amount
        print("\t余额为: " + str(account.balance))
    else:
        print(threading.current_thread().name\
            + "取钱失败！余额不足！")
# 创建一个账户
acct = Account.Account("1234567" , 1000)
# 模拟两个线程对同一个账户取钱
threading.Thread(name='甲', target=draw , args=(acct , 800)).start()
threading.Thread(name='乙', target=draw , args=(acct , 800)).start()
```

先不要管程序中第 12 行被注释掉的代码，上面程序是一个非常简单的取钱逻辑，这个取钱逻辑与实际的取钱操作也很相似。

多次运行上面程序，很有可能都会看到下面的错误结果。

    甲取钱成功！吐出钞票:800
    乙取钱成功！吐出钞票:800
    余额为: 200
    余额为: -800

所示的运行结果并不是银行所期望的结果（不过有可能看到正确的运行结果），这正是多线程编程突然出现的“偶然” 错误因为线程调度的不确定性。

假设系统线程调度器在注释代码处暂停，让另一个线程执行（为了强制暂停，只要取消程序中注释代码前的注释即可）。取消注释后，再次运行程序，将总可以看到所示的错误结果。

问题出现了，账户余额只有 1000 元时取出了 1600 元，而且账户余额出现了负值，远不是银行所期望的结果。虽然上面程序是人为地使用 `time.sleep(0.001)` 来强制线程调度切换，但这种切换也是完全可能发生的（100000 次操作只要有 1 次出现了错误，那就是由编程错误引起的）。

<br/>

# Python互斥锁同步线程

<br/>

之所以出现错误结果，是因为 `run()` 方法的方法体不具有线程安全性，程序中有两个并发线程在修改 Account 对象，而且系统恰好在注释代码处执行线程切换，切换到另一个修改 Account 对象的线程，所以就出现了问题。

为了解决这个问题，Python 的 threading 模块引入了互斥锁（Lock）。threading 模块提供了 
- Lock 「同步锁」
- RLock 「递归锁」

两个类，它们都提供了如下两个方法来加互斥锁和释放互斥锁：

- acquire(blocking=True, timeout=-1)：请求对 Lock 或 RLock 加锁，其中 timeout 参数指定加锁多少秒。
- release()：释放锁。

Lock 和 RLock 的区别如下：

- threading.Lock：它是一个基本的锁对象，每次只能锁定一次，其余的锁请求，需等待锁释放后才能获取。
- threading.RLock：它代表可重入锁（Reentrant Lock）。对于可重入锁，在同一个线程中可以对它进行多次锁定，也可以多次释放。如果使用 RLock，那么 acquire() 和 release() 方法必须成对出现。如果调用了 n 次 acquire() 加锁，则必须调用 n 次 release() 才能释放锁。

由此可见，RLock 锁具有可重入性。也就是说，同一个线程可以对已被加锁的 RLock 锁再次加锁，RLock 对象会维持一个计数器来追踪 acquire() 方法的嵌套调用，线程在每次调用 acquire() 加锁后，都必须显式调用 release() 方法来释放锁。所以，一段被锁保护的方法可以调用另一个被相同锁保护的方法。

RLock 支持在同一线程中多次请求同一资源。

Lock 是控制多个线程对共享资源进行访问的工具。通常，锁提供了对共享资源的独占访问，每次只能有一个线程对 Lock 对象加锁，线程在开始访问共享资源之前应先请求获得 Lock 对象。当对共享资源访问完成后，程序释放对 Lock 对象的锁定。

## Lock用法


锁通常被用来实现对共享资源的同步访问。为每一个共享资源创建一个Lock对象，当你需要访问该资源时，调用acquire方法来获取锁对象（如果其它线程已经获得了该锁，则当前线程需等待其被释放），待资源访问完后，再调用release方法释放锁。

下面我们来举个例子说明如果多线程在没有同步锁的情况下访问公共资源会导致什么情况

```python
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
```

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

```python
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
```

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

## RLock 用法

在实现线程安全的控制中，比较常用的是 RLock。

通常使用 RLock 的代码格式如下：

```python
class X:
    #定义需要保证线程安全的方法
    def m () :
        #加锁
        self.lock.acquire()
        try :
            #需要保证线程安全的代码
            ＃...方法体
        #使用finally 块来保证释放锁
        finally :
            #修改完成，释放锁
            self.lock.release()
```

使用 RLock 对象来控制线程安全，当加锁和释放锁出现在不同的作用范围内时，通常建议使用 finally 块来确保在必要时释放锁。

通过使用 Lock 对象可以非常方便地实现线程安全的类，线程安全的类具有如下特征：

- 该类的对象可以被多个线程安全地访问。
- 每个线程在调用该对象的任意方法之后，都将得到正确的结果。
- 每个线程在调用该对象的任意方法之后，该对象都依然保持合理的状态。

总的来说，不可变类总是线程安全的，因为它的对象状态不可改变；但可变对象需要额外的方法来保证其线程安全。例如，上面的 Account 就是一个可变类，它的 `self.account_no` 和 `self._balance`（为了更好地封装，将 balance 改名为 _balance）两个成员变量都可以被改变，当两个钱程同时修改 Account 对象的 `self._balance` 成员变量的值时，程序就出现了异常。下面将 Account 类对 `self.balance` 的访问设置成线程安全的，那么只需对修改 self.balance 的方法增加线程安全的控制即可。

将 Account 类改为如下形式，它就是线程安全的：

```python
import threading
import time
class Account:
    # 定义构造器
    def __init__(self, account_no, balance):
        # 封装账户编号、账户余额的两个成员变量
        self.account_no = account_no
        self._balance = balance
        self.lock = threading.RLock()
    # 因为账户余额不允许随便修改，所以只为self._balance提供getter方法
    def getBalance(self):
        return self._balance
    # 提供一个线程安全的draw()方法来完成取钱操作
    def draw(self, draw_amount):
        # 加锁
        self.lock.acquire()
        try:
            # 账户余额大于取钱数目
            if self._balance >= draw_amount:
                # 吐出钞票
                print(threading.current_thread().name\
                    + "取钱成功！吐出钞票:" + str(draw_amount))
                time.sleep(0.001)
                # 修改余额
                self._balance -= draw_amount
                print("\t余额为: " + str(self._balance))
            else:
                print(threading.current_thread().name\
                    + "取钱失败！余额不足！")
        finally:
            # 修改完成，释放锁
            self.lock.release()
```

上面程序中的定义了一个 RLock 对象。在程序中实现 `draw()` 方法时，进入该方法开始执行后立即请求对 RLock 对象加锁，当执行完 `draw()` 方法的取钱逻辑之后，程序使用 finally 块来确保释放锁。

程序中 RLock 对象作为同步锁，线程每次开始执行 `draw()` 方法修改 self.balance 时，都必须先对 RLock 对象加锁。当该线程完成对 self._balance 的修改，将要退出 `draw()` 方法时，则释放对 RLock 对象的锁定。这样的做法完全符合“加锁→修改→释放锁”的安全访问逻辑。

当一个线程在 `draw()` 方法中对 RLock 对象加锁之后，其他线程由于无法获取对 RLock 对象的锁定，因此它们同时执行 `draw()` 方法对 self._balance 进行修改。这意味着，并发线程在任意时刻只有一个线程可以进入修改共享资源的代码区（也被称为临界区），所以在同一时刻最多只有一个线程处于临界区内，从而保证了线程安全。

为了保证 Lock 对象能真正“锁定”它所管理的 Account 对象，程序会被编写成每个 Account 对象有一个对应的 Lock（就像一个房间有一个锁一样）。

上面的 Account 类增加了一个代表取钱的 `draw()` 方法，并使用 Lock 对象保证该 `draw()` 方法的线程安全，而且取消了 setBalance() 方法（避免程序直接修改 self._balance 成员变量），因此线程执行体只需调用 Account 对象的 `draw()` 方法即可执行取钱操作。

下面程序创建并启动了两个取钱线程：

```python
import threading
import Account
# 定义一个函数来模拟取钱操作
def draw(account, draw_amount):
    # 直接调用account对象的draw()方法来执行取钱操作
    account.draw(draw_amount)
# 创建一个账户
acct = Account.Account("1234567" , 1000)
# 模拟两个线程对同一个账户取钱
threading.Thread(name='甲', target=draw , args=(acct , 800)).start()
threading.Thread(name='乙', target=draw , args=(acct , 800)).start()
```

上面程序中代表线程执行体的 `draw()` 函数无须自己实现取钱操作，而是直接调用 account 的 `draw()` 方法来执行取钱操作。由于 `draw()` 方法己经使用 RLock 对象实现了线程安全，因此上面程序就不会导致线程安全问题。

可变类的线程安全是以降低程序的运行效率作为代价的，为了减少线程安全所带来的负面影响，程序可以采用如下策略：

- 不要对线程安全类的所有方法都进行同步，只对那些会改变竞争资源（竞争资源也就是共享资源）的方法进行同步。例如，上面 Account 类中的 account_no 实例变量就无须同步，所以程序只对 draw() 方法进行了同步控制。

- 如果可变类有两种运行环境，单线程环境和多线程环境，则应该为该可变类提供两种版本，即线程不安全版本和线程安全版本。在单线程环境中使用钱程不安全版本以保证性能，在多线程环境中使用线程安全版本。

<br/>

# 死锁

<br/>

当两个线程相互等待对方释放资源时，就会发生死锁。Python 解释器没有监测，也不会主动采取措施来处理死锁情况，所以在进行多线程编程时应该采取措施避免出现死锁。
一旦出现死锁，整个程序既不会发生任何异常，也不会给出任何提示，只是所有线程都处于阻塞状态，无法继续。

死锁是很容易发生的，尤其是在系统中出现多个同步监视器的情况下，如下程序将会出现死锁：

```python
import threading
import time
class A:
    def __init__(self):
        self.lock = threading.RLock()
    def foo(self, b):
        try:
            self.lock.acquire()
            print("当前线程名: " + threading.current_thread().name\
                + " 进入了A实例的foo()方法" )     # ①
            time.sleep(0.2)
            print("当前线程名: " + threading.current_thread().name\
                + " 企图调用B实例的last()方法")   # ③
            b.last()
        finally:
            self.lock.release()
    def last(self):
        try:
            self.lock.acquire()
            print("进入了A类的last()方法内部")
        finally:
            self.lock.release()
class B:
    def __init__(self):
        self.lock = threading.RLock()
    def bar(self, a):
        try:
            self.lock.acquire()
            print("当前线程名: " + threading.current_thread().name\
                + " 进入了B实例的bar()方法" )   # ②
            time.sleep(0.2)
            print("当前线程名: " + threading.current_thread().name\
                + " 企图调用A实例的last()方法")  # ④
            a.last()
        finally:
            self.lock.release()
    def last(self):
        try:
            self.lock.acquire()
            print("进入了B类的last()方法内部")
        finally:
            self.lock.release()
a = A()
b = B()
def init():
    threading.current_thread().name = "主线程"
    # 调用a对象的foo()方法
    a.foo(b)
    print("进入了主线程之后")
def action():
    threading.current_thread().name = "副线程"
    # 调用b对象的bar()方法
    b.bar(a)
    print("进入了副线程之后")
# 以action为target启动新线程
threading.Thread(target=action).start()
# 调用init()函数
init()
```
程序既无法向下执行，也不会抛出任何异常，就一直“僵持”着。究其原因，是因为上面程序中 A 对象和 B 对象的方法都是线程安全的方法。

死锁是不应该在程序中出现的，在编写程序时应该尽量避免出现死锁。下面有几种常见的方式用来解决死锁问题：

1. 避免多次锁定。尽量避免同一个线程对多个 Lock 进行锁定。例如上面的死锁程序，主线程要对 A、B 两个对象的 Lock 进行锁定，副线程也要对 A、B 两个对象的 Lock 进行锁定，这就埋下了导致死锁的隐患。

2. 具有相同的加锁顺序。如果多个线程需要对多个 Lock 进行锁定，则应该保证它们以相同的顺序请求加锁。比如上面的死锁程序，主线程先对 A 对象的 Lock 加锁，再对 B 对象的 Lock 加锁；而副线程则先对 B 对象的 Lock 加锁，再对 A 对象的 Lock 加锁。这种加锁顺序很容易形成嵌套锁定，进而导致死锁。如果让主线程、副线程按照相同的顺序加锁，就可以避免这个问题。

3. 使用定时锁。程序在调用 acquire() 方法加锁时可指定 timeout 参数，该参数指定超过 timeout 秒后会自动释放对 Lock 的锁定，这样就可以解开死锁了。

4. 死锁检测。死锁检测是一种依靠算法机制来实现的死锁预防机制，它主要是针对那些不可能实现按序加锁，也不能使用定时锁的场景的。

另外如何在 debug 中监测到 「死锁」，请看我下面的博文中的死锁。

- [python threading 多线程](https://benpaodewoniu.github.io/2018/12/20/python42/)