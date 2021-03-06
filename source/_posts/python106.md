---
title: python | 使用进程池管理进程
date: 2020-05-23 18:08:43
categories:
- python
- 进阶
- 多进程 & 多线程
tags:
- multiprocess
- 多进程
- 多线程
---
和选用线程池来关系多线程类似，当程序中设置到多进程编程时，Python 提供了更好的管理多个进程的方式，就是使用进程池。

<!-- more -->

进程池可以提供指定数量的进程给用户使用，即当有新的请求提交到进程池中时，如果池未满，则会创建一个新的进程用来执行该请求；反之，如果池中的进程数已经达到规定最大值，那么该请求就会等待，只要池中有进程空闲下来，该请求就能得到执行。

Python multiprocessing 模块提供了 Pool() 函数，专门用来创建一个进程池，该函数的语法格式如下：

- multiprocessing.Pool(processes)

其中，processes 参数用于指定该进程池中包含的进程数。如果进程是 None，则默认使用 os.cpu_count() 返回的数字（根据本地的 cpu 个数决定，processes 小于等于本地的 cpu 个数）。

注意，Pool() 函数只是用来创建进程池，而 multiprocessing 模块中表示进程池的类是 multiprocessing.pool.Pool 类。

multiprocessing 模块 Pool 类常用方法

<br/>

|方法名|	功能|
|---|---|
|apply( func[, args[, kwds]] )|	将 func 函数提交给进程池处理。其中 args 代表传给 func 的位置参数，kwds 代表传给 func 的关键字参数。该方法会被阻塞直到 func 函数执行完成。|
|apply_async( func[, args[, kwds[, callback[, error_callback]]]] )|	这是 apply() 方法的异步版本，该方法不会被阻塞。其中 callback 指定 func 函数完成后的回调函数，error_callback 指定 func 函数出错后的回调函数。|
|map( func, iterable[, chunksize] )|	类似于 Python 的 map() 全局函数，只不过此处使用新进程对 iterable 的每一个元素执行 func 函数。|
|map_async( func, iterable[, chunksize[, callback[, error_callback]]] )|	这是 map() 方法的异步版本，该方法不会被阻塞。其中 callback 指定 func 函数完成后的回调函数，error_callback 指定 func 函数出错后的回调函数。|
|imap( func, iterable[, chunksize] )|	这是 map() 方法的延迟版本。|
|imap_unordered( func, iterable[, chunksize] )|	功能类似于 imap() 方法，但该方法不能保证所生成的结果（包含多个元素）与原 iterable 中的元素顺序一致。|
|starmap( func, iterable[,chunksize] )|	功能类似于 map() 方法，但该方法要求 iterable 的元素也是 iterable 对象，程序会将每一个元素解包之后作为 func 函数的参数。|
|close()|	关闭进程池。在调用该方法之后，该进程池不能再接收新任务，它会把当前进程池中的所有任务执行完成后再关闭自己。|
|terminate()|	立即中止进程池。|
|join()|	等待所有进程完成。|

下面程序演示了进程池的创建和使用。

```python
from multiprocessing import Pool
import time
import os
def action(name='http://c.biancheng.net'):
    print(name,' --当前进程：',os.getpid())
    time.sleep(3)
if __name__ == '__main__':
    #创建包含 4 条进程的进程池
    pool = Pool(processes=4)
    # 将action分3次提交给进程池
    pool.apply_async(action)
    pool.apply_async(action, args=('http://c.biancheng.net/python/', ))
    pool.apply_async(action, args=('http://c.biancheng.net/java/', ))
    pool.apply_async(action, kwds={'name': 'http://c.biancheng.net/shell/'})
    pool.close()
    pool.join()
```

程序执行结果为：
    
    http://c.biancheng.net  --当前进程： 14396
    http://c.biancheng.net/python/  --当前进程： 5432
    http://c.biancheng.net/java/  --当前进程： 11080
    http://c.biancheng.net/shell/  --当前进程： 10944

除此之外，我们可以使用 with 语句来管理进程池，这意味着我们无需手动调用 close() 方法关闭进程池。例如：

```python
from multiprocessing import Pool
import time
import os
def action(name='http://c.biancheng.net'):
    time.sleep(3)
    return (name+' --当前进程：%d'%os.getpid())
if __name__ == '__main__':
    #创建包含 4 条进程的进程池
    with Pool(processes=4) as pool:
        adds = pool.map(action, ('http://c.biancheng.net/python/', 'http://c.biancheng.net/java/', 'http://c.biancheng.net/shell/'))
    for arc in adds:
        print(arc)
```

程序执行结果为：
    
    http://c.biancheng.net/python/ --当前进程：24464
    http://c.biancheng.net/java/ --当前进程：22900
    http://c.biancheng.net/shell/ --当前进程：23324