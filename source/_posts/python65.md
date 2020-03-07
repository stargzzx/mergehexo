---
title: python-traceback
date: 2019-11-10 21:39:13
categories:
- python
tags:
- python
- traceback
---
这个是追踪错误的很牛逼的方法。

<!-- more -->

<br/>

# 引子

<br/>

在项目中，我遇见了一个问题，我们会把状态用 log 输入到文件中，但是，有的输出并不知道哪里错了，也就是不知道哪个文件错了。

而且，有的时候，服务器遇到了错误，在本地可能遇不到，所以，我们有了追踪服务器的错误路径的需求。

别人教我用 traceback

以下是摘抄

刚接触Python的时候，简单的异常处理已经可以帮助我们解决大多数问题，但是随着逐渐地深入，我们会发现有很多情况下简单的异常处理已经无法解决问题了，如下代码，单纯的打印异常所能提供的信息会非常有限。

{% codeblock %}
def func1():
  raise Exception("--func1 exception--")
 
def main():
  try:
    func1()
  except Exception as e:
    print e
 
if __name__ == '__main__':
  main()
{% endcodeblock %}
执行后输出如下

	--func1 exception--
	
通过示例，我们发现普通的打印异常只有很少量的信息（通常是异常的value值），这种情况下我们很难定位在哪块代码出的问题，以及如何出现这种异常。那么到底要如何打印更加详细的信息呢？下面我们就来一一介绍。

<br/>

# sys.exc_info和traceback object

<br/>

Python程序的traceback信息均来源于一个叫做traceback object的对象，而这个traceback object通常是通过函数sys.exc_info()来获取的，先来看一个例子：

{% codeblock %}
import sys
def func1():
  raise NameError("--func1 exception--")
def main():
  try:
    func1()
  except Exception as e:
    exc_type, exc_value, exc_traceback_obj = sys.exc_info()
    print "exc_type: %s" % exc_type
    print "exc_value: %s" % exc_value
    print "exc_traceback_obj: %s" % exc_traceback_obj
if __name__ == '__main__':
  main()
{% endcodeblock %}

执行后输出如下：

	exc_type: <type 'exceptions.NameError'>
	exc_value: --func1 exception--
	exc_traceback_obj: <traceback object at 0x7faddf5d93b0>

通过以上示例我们可以看出，sys.exc_info()获取了当前处理的exception的相关信息，并返回一个元组，元组的第一个数据是异常的类型(示例是NameError类型)，第二个返回值是异常的value值，第三个就是我们要的traceback object.

有了traceback object我们就可以通过traceback module来打印和格式化traceback的相关信息，下面我们就来看下traceback module的相关函数。

<br/>

# traceback module

<br/>

Python的traceback module提供一整套接口用于提取，格式化和打印Python程序的stack traces信息，下面我们通过例子来详细了解下这些接口：

## print_tb

{% codeblock %}
import sys
import traceback
 
def func1():
  raise NameError("--func1 exception--")
 
def main():
  try:
    func1()
  except Exception as e:
    exc_type, exc_value, exc_traceback_obj = sys.exc_info()
    traceback.print_tb(exc_traceback_obj)
 
if __name__ == '__main__':
  main()
{% endcodeblock %}

输出：

	File "<ipython-input-23-52bdf2c9489c>", line 11, in main
		func1()
	File "<ipython-input-23-52bdf2c9489c>", line 6, in func1
		raise NameError("--func1 exception--")
		
这里我们可以发现打印的异常信息更加详细了，下面我们了解下print_tb的详细信息：

traceback.print_tb(tb[, limit[, file]])
	
	tb: 这个就是traceback object, 是我们通过sys.exc_info获取到的
	limit: 这个是限制stack trace层级的，如果不设或者为None，就会打印所有层级的stack trace
	file: 这个是设置打印的输出流的，可以为文件，也可以是stdout之类的file-like object。如果不设或为None，则输出到sys.stderr。
	
## print_exception

{% codeblock %}
import sys
import traceback
 
def func1():
  raise NameError("--func1 exception--")
 
def func2():
  func1()
def main():
  try:
    func2()
  except Exception as e:
    exc_type, exc_value, exc_traceback_obj = sys.exc_info()
    traceback.print_exception(exc_type, exc_value, exc_traceback_obj, limit=2, file=sys.stdout)
 
if __name__ == '__main__':
  main()
{% endcodeblock %}

输出：

	Traceback (most recent call last):
	  File "<ipython-input-24-a68061acf52f>", line 13, in main
		func2()
	  File "<ipython-input-24-a68061acf52f>", line 9, in func2
		func1()
	NameError: --func1 exception--
	
看下定义：
traceback.print_exception(etype, value, tb[, limit[, file]])

跟print_tb相比多了两个参数etype和value，分别是exception type和exception value，加上tb(traceback object)，正好是sys.exc_info()返回的三个值

另外，与print_tb相比，打印信息多了开头的"Traceback (most...)"信息以及最后一行的异常类型和value信息

还有一个不同是当异常为SyntaxError时，会有"^"来指示语法错误的位置

<br/>

# print_exc

<br/>

print_exc是简化版的print_exception, 由于exception type, value和traceback object都可以通过sys.exc_info()获取，因此print_exc()就自动执行exc_info()来帮助获取这三个参数了，也因此这个函数是我们的程序中最常用的，因为它足够简单

{% codeblock %}
import sys
import traceback
 
def func1():
  raise NameError("--func1 exception--")
 
def func2():
  func1()
 
def main():
  try:
    func2()
  except Exception as e:
    traceback.print_exc(limit=1, file=sys.stdout)
 
if __name__ == '__main__':
  main()
{% endcodeblock %}

输出（由于limit=1，因此只有一个层级被打印出来）：

	Traceback (most recent call last):
	  File "<ipython-input-25-a1f5c73b97c4>", line 13, in main
		func2()
	NameError: --func1 exception--
	
定义如下：traceback.print_exc([limit[, file]])

只有两个参数，够简单

<br/>

# format_exc

<br/>

{% codeblock %}
import logging
import sys
import traceback
logger = logging.getLogger("traceback_test")
 
def func1():
  raise NameError("--func1 exception--")
 
def func2():
  func1()
 
def main():
  try:
    func2()
  except Exception as e:
    logger.error(traceback.format_exc(limit=1, file=sys.stdout))
 
if __name__ == '__main__':
  main()
{% endcodeblock %}

从这个例子可以看出有时候我们想得到的是一个字符串，比如我们想通过logger将异常记录在log里，这个时候就需要format_exc了，这个也是最常用的一个函数，它跟print_exc用法相同，只是不直接打印而是返回了字符串。

traceback module中还有一些其它的函数，但因为并不常用，就不在展开来讲，感兴趣的同学可以看下参考链接中的文档。

<br/>

# 获取线程中的异常信息

<br/>

通常情况下我们无法将多线程中的异常带回主线程，所以也就无法打印线程中的异常，而通过上边学到这些知识，我们可以对线程做如下修改，从而实现捕获线程异常的目的。

以下示例来自weidong的博客文章，稍有修改（见参考链接）

{% codeblock %}
import threading
import traceback
 
def my_func():
  raise BaseException("thread exception")
 
 
class ExceptionThread(threading.Thread):
 
  def __init__(self, group=None, target=None, name=None, args=(), kwargs=None, verbose=None):
    """
    Redirect exceptions of thread to an exception handler.
    """
    threading.Thread.__init__(self, group, target, name, args, kwargs, verbose)
    if kwargs is None:
      kwargs = {}
    self._target = target
    self._args = args
    self._kwargs = kwargs
    self._exc = None
 
  def run(self):
    try:
      if self._target:
        self._target()
    except BaseException as e:
      import sys
      self._exc = sys.exc_info()
    finally:
      #Avoid a refcycle if the thread is running a function with
      #an argument that has a member that points to the thread.
      del self._target, self._args, self._kwargs
 
  def join(self):
    threading.Thread.join(self)
    if self._exc:
      msg = "Thread '%s' threw an exception: %s" % (self.getName(), self._exc[1])
      new_exc = Exception(msg)
      raise new_exc.__class__, new_exc, self._exc[2]
 
 
t = ExceptionThread(target=my_func, name='my_thread')
t.start()
try:
  t.join()
except:
  traceback.print_exc()
{% endcodeblock %}

输出如下：

	Traceback (most recent call last):
	  File "/data/code/testcode/thread_exc.py", line 43, in <module>
		t.join()
	  File "/data/code/testcode/thread_exc.py", line 23, in run
		self._target()
	  File "/data/code/testcode/thread_exc.py", line 5, in my_func
		raise BaseException("thread exception")
	Exception: Thread 'my_thread' threw an exception: thread exception