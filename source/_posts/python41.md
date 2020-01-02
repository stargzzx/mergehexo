---
title: python multiprocessing 多进程
date: 2018-12-19 22:12:38
categories:
- python
tags:
- python 第三方库
- python
- multiprocessing
---
如题。
<!-- more -->
## 运行条件
多进程，必须得有

	if __name__ == '__main__':

否则会报错。
{% codeblock %}

import multiprocessing as mp

def job(a,d):
    print("aaaaaa")

if __name__ == '__main__':
    p1 = mp.Process(target=job,args=(1,2))
    p1.start()
    p1.join()
	
		# aaaaaa

{% endcodeblock %}
## queue进程输出
{% codeblock %}

import multiprocessing as mp

def job(q):
    res=0
    for i in range(1000):
        res+=i+i**2+i**3
    q.put(res)    #queue

if __name__=='__main__':
    q = mp.Queue()
    p1 = mp.Process(target=job,args=(q,)) # 如果 args 中只有一个参数，那么我们必须的在后面添加一个逗号
    p2 = mp.Process(target=job,args=(q,))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    res1 = q.get()
    res2 = q.get()
    print(res1+res2)
		# 499667166000

{% endcodeblock %}
## 效率比较 multithreading multiprocessing
{% codeblock %}

import multiprocessing as mp
import threading as td
import time

def job(q):
    res = 0
    for i in range(1000000):
        res += i+i**2+i**3
    q.put(res) # queue

def multicore():
    q = mp.Queue()
    p1 = mp.Process(target=job, args=(q,))
    p2 = mp.Process(target=job, args=(q,))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
    res1 = q.get()
    res2 = q.get()
    print('multicore:' , res1+res2)

def normal():
    res = 0
    for _ in range(2):
        for i in range(1000000):
            res += i+i**2+i**3
    print('normal:', res)

def multithread():
    q = mp.Queue()
    t1 = td.Thread(target=job, args=(q,))
    t2 = td.Thread(target=job, args=(q,))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    res1 = q.get()
    res2 = q.get()
    print('multithread:', res1+res2)

if __name__ == '__main__':
    st = time.time()
    normal()
    st1= time.time()
    print('normal time:', st1 - st)
    multithread()
    st2 = time.time()
    print('multithread time:', st2 - st1)
    multicore()
    print('multicore time:', time.time()-st2)
		# normal: 499999666667166666000000
		# normal time: 1.1491570472717285
		# multithread: 499999666667166666000000
		# multithread time: 1.4985547065734863
		# multicore: 499999666667166666000000
		# multicore time: 0.8846650123596191


{% endcodeblock %}
## 进程池 pool
pool 是一个进程池，我们把值和函数传给进程池，进程池就会自动的分配进程。更为重要的一点是，进程池有返回值。

	pool = mp.Pool()
	
如果 Pool 里面没有参数，则，默认分配所有的核，但是我们可以指定核数，比如两个核

	pool = mp.Pool(processes = 2)
	
{% codeblock %}

import multiprocessing as mp

def job(x):
    return x * x

def multicore():
    pool = mp.Pool()
    res = pool.map(job,range(10)) # 进入进程池开始运算
    print(res)

if __name__ == '__main__':
    multicore()
		# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

{% endcodeblock %}
只输出一个核的结果
{% codeblock %}

import multiprocessing as mp

def job(x):
    return x * x

def multicore():
    pool = mp.Pool(processes=2)
    res = pool.map(job,range(10))
    print(res)
    res = pool.map_async(job,(5,)) # 里面的值为什么有一个逗号呢，因为我们是要传递一个可以迭代的元素，而在这里我们传的是一个元组，元组中如果有一个元素，必须得添加一个逗号，这是一种语法规则
									# 这个打印第五个核得返回值
									# 如果传递多个值，也就是核数，就会报错，如下面这个函数
									# res = pool.map_async(job,(2,3，4，5))
	print(res.get())

if __name__ == '__main__':
    multicore()
		# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
		# [25]
		
{% endcodeblock %}
{% codeblock %}

import multiprocessing as mp

def job(x):
    return x * x

def multicore():
    pool = mp.Pool(processes=2)
    res = pool.map(job,range(10))
    print(res)
    res = pool.map_async(job,(5,))
    print(res.get())
	# 下面这两段代码和 map 的效果一样
    multi_res = [pool.apply_async(job,(i,)) for i in range(10)]
    print([res.get() for res in multi_res])

if __name__ == '__main__':
    multicore()
	
		# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
		# [25]
		# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
	
{% endcodeblock %}
## 共享内存
我们在多个函数中共享一个全局变量，通常会有如下的代码

	global A
	
但是，如果是多核的话，我们是无法通过 global 来共享值的。
共享值，我们会用到共享内存这个途径来解决。代码如下

	Value(typecode_or_type, *args, lock=True)  
		typecode_or_type：定义了返回类型（转换成C语言中存储类型），它要么是一个ctypes类型，要么是一个代表ctypes类型的code。
		*args：开辟一个空间，并赋一个args值，值得类型不限

	mp.Value('i')
		如果是 i 的话，那就是一个整数
			   d 的话，那就是一个小数
			   更多的值如下图
			   
共享数组，在这里尤为注意的是，共享的数组只能是一维的，不能是多维的

	array = mp.Array('i',[1,2,3])
	# 下面这个是不合法的
	array = mp.Array('i',[[1,2,3]])
			   
{% img /images/python/41_0.png %}
## lock 锁
