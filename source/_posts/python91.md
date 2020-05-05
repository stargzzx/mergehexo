---
title: python | 日志重复写的问题
date: 2020-05-05 17:07:01
categories:
- [python,进阶,日志]
- [python,模块,logger]
tags:
- python
---
还别说，我还真在项目中遇到了这个问题，只不过，当时我太过于稚嫩，是部门老大解决的。

<!-- more -->

## 参考资料

- [python logging 重复写日志问题](https://blog.csdn.net/huilan_same/article/details/51858817)

用Python的logging模块记录日志时，遇到了重复记录日志的问题，第一条记录写一次，第二条记录写两次，第三条记录写三次。。。很头疼，这样记日志可不行。网上搜索到了原因与解决方案：

- 原因：没有移除handler
- 解决：在日志记录完之后removeHandler

修改前示例代码：

## 方法一

```python
import logging


def log(message):
    logger = logging.getLogger('testlog')

    streamhandler = logging.StreamHandler()
    streamhandler.setLevel(logging.ERROR)
    formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(name)s - %(message)s')
    streamhandler.setFormatter(formatter)

    logger.addHandler(streamhandler)
    logger.error(message)

if __name__ == '__main__':
    log('hi')
    log('hi too')
    log('hi three')
```

修改前输出结果：

	2016-07-08 09:17:29,740 - ERROR - testlog - hi
	2016-07-08 09:17:29,740 - ERROR - testlog - hi too
	2016-07-08 09:17:29,740 - ERROR - testlog - hi too
	2016-07-08 09:17:29,740 - ERROR - testlog - hi three
	2016-07-08 09:17:29,740 - ERROR - testlog - hi three
	2016-07-08 09:17:29,740 - ERROR - testlog - hi three

修改后示例代码：

## 方法二

```python
import logging


def log(message):
    logger = logging.getLogger('testlog')

    streamhandler = logging.StreamHandler()
    streamhandler.setLevel(logging.ERROR)
    formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(name)s - %(message)s')
    streamhandler.setFormatter(formatter)

    logger.addHandler(streamhandler)
    logger.error(message)

    #  添加下面一句，在记录日志之后移除句柄
    logger.removeHandler(streamhandler)

if __name__ == '__main__':
    log('hi')
    log('hi too')
    log('hi three')
```

修改后输出结果：

	2016-07-08 09:32:28,206 - ERROR - testlog - hi
	2016-07-08 09:32:28,206 - ERROR - testlog - hi too
	2016-07-08 09:32:28,206 - ERROR - testlog - hi three

深度解析：
Google之后，大概搞明白了，就是你第二次调用log的时候，根据getLogger(name)里的name获取同一个logger，而这个logger里已经有了第一次你添加的handler，第二次调用又添加了一个handler，所以，这个logger里有了两个同样的handler，以此类推，调用几次就会有几个handler。。

所以这里有以下几个解决办法：

1. 每次创建不同name的logger，每次都是新logger，不会有添加多个handler的问题。（ps:这个办法太笨，不过我之前就是这么干的。。）
2. 像上面一样每次记录完日志之后，调用removeHandler()把这个logger里的handler移除掉。
3. 在log方法里做判断，如果这个logger已有handler，则不再添加handler。
4. 与方法2一样，不过把用pop把logger的handler列表中的handler移除。

下面是方法3与方法4的代码示例：

## 方法三

```python
import logging


def log(message):
    logger = logging.getLogger('testlog')

    #  这里进行判断，如果logger.handlers列表为空，则添加，否则，直接去写日志
    if not logger.handlers:
        streamhandler = logging.StreamHandler()
        streamhandler.setLevel(logging.ERROR)
        formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(name)s - %(message)s')
        streamhandler.setFormatter(formatter)
        logger.addHandler(streamhandler)

    logger.error(message)


if __name__ == '__main__':
    log('hi')
    log('hi too')
    log('hi three')
```

## 方法四

```python
import logging


def log(message):
    logger = logging.getLogger('testlog')

    streamhandler = logging.StreamHandler()
    streamhandler.setLevel(logging.ERROR)
    formatter = logging.Formatter('%(asctime)s - %(levelname)s - %(name)s - %(message)s')
    streamhandler.setFormatter(formatter)

    logger.addHandler(streamhandler)

    logger.error(message)

    #  用pop方法把logger.handlers列表中的handler移除，注意如果你add了多个handler，这里需多次pop，或者可以直接为handlers列表赋空值
    logger.handlers.pop()
    # logger.handler = []


if __name__ == '__main__':
    log('hi')
    log('hi too')
    log('hi three')
```
