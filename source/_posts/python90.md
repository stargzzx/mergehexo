---
title: python | 日志基础
date: 2020-05-05 10:58:16
categories:
- [python,进阶,日志]
- [python,模块,logger]
tags:
- python
---
日志，是从菜鸟走向大佬的必备之路。

- 从硬看代码 转向 debug
- 从 print 转向 log

这个时候，你才具备正确的开发思维。

<!-- more -->

<br/>

# 参考资料

<br/>

- [参考资料](https://www.cnblogs.com/qianyuliang/p/7234217.html)

<br/>

# logging模块简介

<br/>

logging模块是Python内置的标准模块，主要用于

- 输出运行日志
- 设置输出日志的等级
- 日志保存路径
- 日志文件回滚等；

相比print，具备如下优点：

可以通过设置不同的日志等级，在release版本中只输出重要信息，而不必显示大量的调试信息；

print 将所有信息都输出到标准输出中，严重影响开发者从标准输出中查看其它数据；

logging 则可以由开发者决定将信息输出到什么地方，以及怎么输出；

Logger从来不直接实例化，经常通过logging模块级方法（Module-Level  Function）`logging.getLogger(name)` 来获得，其中如果name不给定就用root。名字是以点号分割的命名方式命名的(a.b.c)。对同一个名字的多个调用 `logging.getLogger()` 方法会返回同一个logger对象。这种命名方式里面，后面的loggers是前面logger的子logger，自动继承父loggers的log信息，正因为此,没有必要把一个应用的所有logger都配置一遍，只要把顶层的logger配置好了，然后子logger根据需要继承就行了。

logging.Logger对象扮演了三重角色:

1. 首先,它暴露给应用几个方法以便应用可以在运行时写log.
2. 其次,Logger对象按照log信息的严重程度或者根据filter对象来决定如何处理log信息(默认的过滤功能).
3. 最后,logger还负责把log信息传送给相关的handlers.

<br/>

# logging模块使用

<br/>

## 基本使用

配置logging基本的设置，然后在控制台输出日志，

```python
import logging
logging.basicConfig(level = logging.INFO,format = '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)
 
logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")
```

运行时，控制台输出，

```
2016-10-09 19:11:19,434 - __main__ - INFO - Start print log
2016-10-09 19:11:19,434 - __main__ - WARNING - Something maybe fail.
2016-10-09 19:11:19,434 - __main__ - INFO - Finish
```

logging中可以选择很多消息级别，如：

- DEBUG
- INFO
- WARNING
- ERROR
- CRITICAL

通过赋予logger或者handler不同的级别，开发者就可以只输出错误信息到特定的记录文件，或者在调试时只记录调试信息。

将logger的级别改为DEBUG，再观察一下输出结果

```python
logging.basicConfig(level = logging.DEBUG,format = '%(asctime)s - %(name)s - %(levelname)s - %(message)s')
```

从输出结果可以看到，输出了debug的日志记录

```
2016-10-09 19:12:08,289 - __main__ - INFO - Start print log
2016-10-09 19:12:08,289 - __main__ - DEBUG - Do something
2016-10-09 19:12:08,289 - __main__ - WARNING - Something maybe fail.
2016-10-09 19:12:08,289 - __main__ - INFO - Finish
```

logging.basicConfig函数各参数：

|格式|	描述|
|---|---|
|filename| 指定日志文件名；|
|filemode| 和file函数意义相同，指定日志文件的打开模式，'w'或者'a'；|
|format| 指定输出的格式和内容，format可以输出很多有用的信息，|
|datefmt| 指定时间格式，同time.strftime()；|
|level| 设置日志级别，默认为logging.WARNNING；|
|stream| 指定将日志的输出流，可以指定输出到sys.stderr，sys.stdout或者文件，默认输出到sys.stderr，当stream和filename同时指定时，stream被忽略；|

Formatters定义了Logger记录的输出格式。

定义了最终log信息的内容格式，应用可以直接实例化Foamatter类。信息格式字符串用 `%(<dictionary key>)s` 风格的字符串做替换。

|属性名|	格式|	描述|
|---|---|---|
|asctime|	%(asctime)s|	易读的时间格式： 默认情况下是'2003-07-08 16：49：45,896'的形式（逗号之后的数字是毫秒部分的时间）|
|filename|	%(filename)s|	路径名的文件名部分。|
|funcName|	%(funcName)s|	日志调用所在的函数名|
|levelname|	%(levelname)s|	消息的级别名称('DEBUG', 'INFO', 'WARNING', 'ERROR', 'CRITICAL').|
|levelno|	%(levelno)s|	对应数字格式的日志级别 (DEBUG, INFO, WARNING, ERROR,CRITICAL).|
|lineno|	%(lineno)d|	发出日志记录调用的源码行号 (如果可用)。|
|module|	%(module)s|	所在的模块名(如test6.py模块则记录test6)|
|message|	%(message)s|	记录的信息|
|name|	%(name)s|	调用的logger记录器的名称|
|process|	%(process)d|	进程ID|
|processName|	%(processName)s|	进程名|
|thread|	%(thread)d|	线程ID|
|threadName|	%(threadName)s|	线程名|

<br/>

# 将日志写入到文件

<br/>

## 将日志写入到文件

设置logging，创建一个FileHandler，并对输出消息的格式进行设置，将其添加到logger，然后将日志写入到指定的文件中，「日志文件会自动创建，但是，文件夹需要自己先行创建」

```python
import logging
logger = logging.getLogger(__name__)
logger.setLevel(level = logging.INFO)
handler = logging.FileHandler("log.txt")
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
logger.addHandler(handler)
 
logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")
```

log.txt中日志数据为：

```
2017-07-25 15:02:09,905 - __main__ - INFO - Start print log
2017-07-25 15:02:09,905 - __main__ - WARNING - Something maybe fail.
2017-07-25 15:02:09,905 - __main__ - INFO - Finish
```

上面的文件方式采用的是追加，如果想要重写需要

	handler = logging.FileHandler("log.txt",mode='w')

## 将日志同时输出到屏幕和日志文件

logger中添加StreamHandler，可以将日志输出到屏幕上

```python
import logging

logger = logging.getLogger(__name__)
logger.setLevel(level=logging.INFO)
handler = logging.FileHandler("log.txt")
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)

console = logging.StreamHandler()
console.setLevel(logging.INFO)
console.setFormatter(formatter)

logger.addHandler(handler)
logger.addHandler(console)

logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")
```

可以在log.txt文件和控制台中看到

```
2017-07-25 15:03:05,075 - __main__ - INFO - Start print log
2017-07-25 15:03:05,075 - __main__ - WARNING - Something maybe fail.
2017-07-25 15:03:05,075 - __main__ - INFO - Finish
```

可以发现，logging有一个日志处理的主对象，其他处理方式都是通过addHandler添加进去，logging中包含的handler主要有如下几种，

|handler名称 |位置| 作用|
|---|---|---|
|StreamHandler| logging.StreamHandler| 日志输出到流，可以是sys.stderr，sys.stdout或者文件|
|FileHandler| logging.FileHandler| 日志输出到文件|
|BaseRotatingHandler| logging.handlers.BaseRotatingHandler|基本的日志回滚方式|
|RotatingHandler| logging.handlers.RotatingHandler|日志回滚方式，支持日志文件最大数量和日志文件回滚|
|TimeRotatingHandler| logging.handlers.TimeRotatingHandler|日志回滚方式，在一定时间区域内回滚日志文件|
|SocketHandler| logging.handlers.SocketHandler|远程输出日志到TCP/IP sockets|
|DatagramHandler| logging.handlers.DatagramHandler|远程输出日志到UDP sockets|
|SMTPHandler| logging.handlers.SMTPHandler|远程输出日志到邮件地址|
|SysLogHandler| logging.handlers.SysLogHandler|日志输出到syslog|
|NTEventLogHandler| logging.handlers.NTEventLogHandler|远程输出日志到Windows NT/2000/XP的事件日志|
|MemoryHandler| logging.handlers.MemoryHandler|日志输出到内存中的指定buffer|
|HTTPHandler| logging.handlers.HTTPHandler|通过"GET"或者"POST"远程输出到HTTP服务器|

## 日志回滚

使用RotatingFileHandler，可以实现日志回滚，

```python
import logging
from logging.handlers import RotatingFileHandler
logger = logging.getLogger(__name__)
logger.setLevel(level = logging.INFO)
#定义一个RotatingFileHandler，最多备份3个日志文件，每个日志文件最大1K
rHandler = RotatingFileHandler("log.txt",maxBytes = 1*1024,backupCount = 3)
rHandler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
rHandler.setFormatter(formatter)
 
console = logging.StreamHandler()
console.setLevel(logging.INFO)
console.setFormatter(formatter)
 
logger.addHandler(rHandler)
logger.addHandler(console)
 
logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
logger.info("Finish")
```

可以在工程目录中看到，备份的日志文件。

说一下上面备份的过程

首先，文件先输出到 `log.txt`

等 `log.txt` 满了 `1K` 之后，会把这个文件中的内容放到 `log.txt.1` 这个文件中，然后 `log.txt.1` 满 `1K` 后，放到 `log.txt.2` 然后直到 `log.txt.3` 就不再增加了。

那么文件内容是如何传递的呢？

以

	2020-05-05 16:32:35,810 - __main__ - INFO - Start print log

这句话为例，先放在 `log.txt` 中，然后满了转移到 `log.txt.1` 中，`log.txt.1` 满了后，放到 `log.txt.2` 直到放到 `log.txt.3` 然后 `log.txt.3` 满了后，就`丢掉`数据。

也就是，log 一共有 4 个文件，然后加起来共 4K 数据。

## 设置消息的等级

可以设置不同的日志等级，用于控制日志的输出

|日志等级|使用范围|
|---|---|
|FATAL|致命错误|
|CRITICAL|特别糟糕的事情，如内存耗尽、磁盘空间为空，一般很少使用|
|ERROR|发生错误时，如IO操作失败或者连接问题|
|WARNING|发生很重要的事件，但是并不是错误时，如用户登录密码错误|
|INFO|处理请求或者状态变化等日常事务|

`setLevel(lvl)` 定义处理log的最低等级，内建的级别为：

- DEBUG
- INFO
- WARNING
- ERROR
- CRITICAL

下图是级别对应数值

|级别|	数值|
|---|---|
|CRITICAL|	50|
|ERROR|	40|
|WARNING|	30,默认|
|INFO|	20|
|DEBUG|	10|

<br/>

# 捕获traceback

<br/>

Python中的traceback模块被用于跟踪异常返回信息，可以在logging中记录下traceback「`exc_info = True` 使用这个属性作为开启追踪」

```python
import logging
logger = logging.getLogger(__name__)
logger.setLevel(level = logging.INFO)
handler = logging.FileHandler("log.txt")
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
 
console = logging.StreamHandler()
console.setLevel(logging.INFO)
 
logger.addHandler(handler)
logger.addHandler(console)
 
logger.info("Start print log")
logger.debug("Do something")
logger.warning("Something maybe fail.")
try:
    open("sklearn.txt","rb")
except (SystemExit,KeyboardInterrupt):
    raise
except Exception:
    logger.error("Faild to open sklearn.txt from logger.error",exc_info = True)
 
logger.info("Finish")
```

控制台和日志文件log.txt中输出

```
2017-07-25 15:04:24,045 - __main__ - INFO - Start print log
2017-07-25 15:04:24,045 - __main__ - WARNING - Something maybe fail.
2017-07-25 15:04:24,046 - __main__ - ERROR - Faild to open sklearn.txt from logger.error
Traceback (most recent call last):
  File "E:\PYTHON\Eclipse\eclipse\Doc\14day5\Logger模块\Logging.py", line 71, in <module>
    open("sklearn.txt","rb")
IOError: [Errno 2] No such file or directory: 'sklearn.txt'
2017-07-25 15:04:24,049 - __main__ - INFO - Finish
```

也可以使用logger.exception(msg,\_args)，它等价于logger.error(msg,exc_info = True,\_args)，

	将
	logger.error("Faild to open sklearn.txt from logger.error",exc_info = True)
	替换为，
	logger.exception("Failed to open sklearn.txt from logger.exception")

<br/>

# 多模块使用logging

<br/>

主模块mainModule.py

```
import logging
import subModule
logger = logging.getLogger("mainModule")
logger.setLevel(level = logging.INFO)
handler = logging.FileHandler("log.txt")
handler.setLevel(logging.INFO)
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
handler.setFormatter(formatter)
 
console = logging.StreamHandler()
console.setLevel(logging.INFO)
console.setFormatter(formatter)
 
logger.addHandler(handler)
logger.addHandler(console)
 
 
logger.info("creating an instance of subModule.subModuleClass")
a = subModule.SubModuleClass()
logger.info("calling subModule.subModuleClass.doSomething")
a.doSomething()
logger.info("done with  subModule.subModuleClass.doSomething")
logger.info("calling subModule.some_function")
subModule.som_function()
logger.info("done with subModule.some_function")
```

子模块subModule.py

```
import logging
 
module_logger = logging.getLogger("mainModule.sub")
class SubModuleClass(object):
    def __init__(self):
        self.logger = logging.getLogger("mainModule.sub.module")
        self.logger.info("creating an instance in SubModuleClass")
    def doSomething(self):
        self.logger.info("do something in SubModule")
        a = []
        a.append(1)
        self.logger.debug("list a = " + str(a))
        self.logger.info("finish something in SubModuleClass")
 
def som_function():
    module_logger.info("call function some_function")
```

执行之后，在控制和日志文件log.txt中输出

```
2017-07-25 15:05:07,427 - mainModule - INFO - creating an instance of subModule.subModuleClass
2017-07-25 15:05:07,427 - mainModule.sub.module - INFO - creating an instance in SubModuleClass
2017-07-25 15:05:07,427 - mainModule - INFO - calling subModule.subModuleClass.doSomething
2017-07-25 15:05:07,427 - mainModule.sub.module - INFO - do something in SubModule
2017-07-25 15:05:07,427 - mainModule.sub.module - INFO - finish something in SubModuleClass
2017-07-25 15:05:07,427 - mainModule - INFO - done with  subModule.subModuleClass.doSomething
2017-07-25 15:05:07,427 - mainModule - INFO - calling subModule.some_function
2017-07-25 15:05:07,427 - mainModule.sub - INFO - call function some_function
2017-07-25 15:05:07,428 - mainModule - INFO - done with subModule.some_function
```

说明：

首先在主模块定义了 `logger'mainModule'`，并对它进行了配置，就可以在解释器进程里面的其他地方通过 `getLogger('mainModule')` 得到的对象都是一样的，不需要重新配置，可以直接使用。定义的该logger的子logger，都可以共享父logger的定义和配置，所谓的父子logger是通过命名来识别，任意以'mainModule'开头的logger都是它的子logger，例如'mainModule.sub'。

实际开发一个 `application`，首先可以通过logging配置文件编写好这个application所对应的配置，可以生成一个根logger，如'PythonAPP'，然后在主函数中通过fileConfig加载logging配置，接着在application的其他地方、不同的模块中，可以使用根logger的子logger，如'PythonAPP.Core'，'PythonAPP.Web'来进行log，而不需要反复的定义和配置各个模块的logger。

<br/>

# 通过JSON或者YAML文件配置logging模块

<br/>

尽管可以在Python代码中配置logging，但是这样并不够灵活，最好的方法是使用一个配置文件来配置。在Python 2.7及以后的版本中，可以从字典中加载logging配置，也就意味着可以通过JSON或者YAML文件加载日志的配置。

## 通过JSON文件配置

JSON配置文件

```json
{
    "version":1,
    "disable_existing_loggers":false,
    "formatters":{
        "simple":{
            "format":"%(asctime)s - %(name)s - %(levelname)s - %(message)s"
        }
    },
    "handlers":{
        "console":{
            "class":"logging.StreamHandler",
            "level":"DEBUG",
            "formatter":"simple",
            "stream":"ext://sys.stdout"
        },
        "info_file_handler":{
            "class":"logging.handlers.RotatingFileHandler",
            "level":"INFO",
            "formatter":"simple",
            "filename":"info.log",
            "maxBytes":"10485760",
            "backupCount":20,
            "encoding":"utf8"
        },
        "error_file_handler":{
            "class":"logging.handlers.RotatingFileHandler",
            "level":"ERROR",
            "formatter":"simple",
            "filename":"errors.log",
            "maxBytes":10485760,
            "backupCount":20,
            "encoding":"utf8"
        }
    },
    "loggers":{
        "my_module":{
            "level":"ERROR",
            "handlers":["info_file_handler"],
            "propagate":"no"
        }
    },
    "root":{
        "level":"INFO",
        "handlers":["console","info_file_handler","error_file_handler"]
    }
}
```

通过JSON加载配置文件，然后通过logging.dictConfig配置logging，

```python
import json
import logging.config
import os
 
def setup_logging(default_path = "logging.json",default_level = logging.INFO,env_key = "LOG_CFG"):
    path = default_path
    value = os.getenv(env_key,None)
    if value:
        path = value
    if os.path.exists(path):
        with open(path,"r") as f:
            config = json.load(f)
            logging.config.dictConfig(config)
    else:
        logging.basicConfig(level = default_level)
 
def func():
    logging.info("start func")
 
    logging.info("exec func")
 
    logging.info("end func")
 
if __name__ == "__main__":
    setup_logging(default_path = "logging.json")
    func()
```

## 通过YAML文件配置

通过YAML文件进行配置，比JSON看起来更加简介明了，

```yaml
version: 1
disable_existing_loggers: False
formatters:
        simple:
            format: "%(asctime)s - %(name)s - %(levelname)s - %(message)s"
handlers:
    console:
            class: logging.StreamHandler
            level: DEBUG
            formatter: simple
            stream: ext://sys.stdout
    info_file_handler:
            class: logging.handlers.RotatingFileHandler
            level: INFO
            formatter: simple
            filename: info.log
            maxBytes: 10485760
            backupCount: 20
            encoding: utf8
    error_file_handler:
            class: logging.handlers.RotatingFileHandler
            level: ERROR
            formatter: simple
            filename: errors.log
            maxBytes: 10485760
            backupCount: 20
            encoding: utf8
loggers:
    my_module:
            level: ERROR
            handlers: [info_file_handler]
            propagate: no
root:
    level: INFO
    handlers: [console,info_file_handler,error_file_handler]
```

通过YAML加载配置文件，然后通过logging.dictConfig配置logging

```python
import yaml
import logging.config
import os
 
def setup_logging(default_path = "logging.yaml",default_level = logging.INFO,env_key = "LOG_CFG"):
    path = default_path
    value = os.getenv(env_key,None)
    if value:
        path = value
    if os.path.exists(path):
        with open(path,"r") as f:
            config = yaml.load(f)
            logging.config.dictConfig(config)
    else:
        logging.basicConfig(level = default_level)
 
def func():
    logging.info("start func")
 
    logging.info("exec func")
 
    logging.info("end func")
 
if __name__ == "__main__":
    setup_logging(default_path = "logging.yaml")
    func()
```
