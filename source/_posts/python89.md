---
title: python | click 模块
date: 2020-05-05 10:54:12
categories:
- python
- 模块
- click
tags:
- python
---
Click  是用 Python 写的一个第三方模块，用于快速创建命令行。我们知道，Python 内置了一个 Argparse 的标准库用于创建命令行，但使用起来有些繁琐，Click 相比于 Argparse，就好比 requests 相比于 urllib。

<!-- more -->

<br/>

# 官方文档

<br/>

- [github](https://github.com/pallets/click)
- [文档](https://click.palletsprojects.com/en/6.x/)
- [Python命令行神器 Click 简明笔记](https://blog.csdn.net/lihua_tan/article/details/54869355)
- [Python 命令行之旅：深入 click 之子命令篇](https://www.cnblogs.com/xueweihan/p/11931096.html)

<br/>

# 快速使用

<br/>

Click 的使用大致有两个步骤：

- 使用 @click.command()装饰一个函数，使之成为命令行接口；
- 使用 @click.option()等装饰函数，为其添加命令行选项等。

它的一种典型使用形式如下：

```python
import click

@click.command()
@click.option('--param', default=default_value, help='description')
def func(param):
    pass
```

