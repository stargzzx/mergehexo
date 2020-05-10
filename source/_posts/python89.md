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

这篇文章主要包括：

- 初体验
- 参数篇
- 选项篇

<!-- more -->

<br/>

# 初体验

<br/>

- [大家都说好用的 Python 命令行库：click](https://www.cnblogs.com/xueweihan/p/11795284.html)

## 介绍

click 是一个以尽可能少的代码、以组合的方式创建优美的命令行程序的 Python 包。它有很高的可配置性，同时也能开箱即用。

它旨在让编写命令行工具的过程既快速又有趣，还能防止由于无法实现预期的 CLI API 所产生挫败感。它有如下三个特点：

- 任意嵌套命令
- 自动生成帮助
- 支持运行时延迟加载子命令

## 快速开始

### 业务逻辑

首先定义业务逻辑，是不是感觉到有些难以置信呢？

不论是 `argparse` 还是 `docopt`，业务逻辑都是被放在最后一步，但 `click` 却是放在第一步。细想想 `click` 的这种方式才更符合人的思维吧？不论用什么命令行框架，我们最终关心的就是实现业务逻辑，其它的能省则省。

我们以官方示例为例，来介绍 `click` 的用法和哲学。假设命令行程序的输入是 `name` 和 `count`，功能是打印指定次数的名字。

那么在 `hello.py` 中，很容易写出如下代码：

```python
def hello(count, name):
    """Simple program that greets NAME for a total of COUNT times."""
    for x in range(count):
        click.echo('Hello %s!' % name)
```

这段代码的逻辑很简单，就是循环 `count` 次，使用 `click.echo` 打印 `name`。其中，`click.echo` 和 `print` 的作用相似，但功能更加强大，能处理好 Unicode 和 二进制数据的情况。

### 定义参数

很显然，我们需要针对 `count` 和 `name` 来定义它们所对应的参数信息。

- `count` 对应为命令行选项 `--count`，类型为数字，我们希望在不提供参数时，其默认值是 1

- `name` 对应为命令行选项 `--name`，类型为字符串，我们希望在不提供参数时，能给人提示

使用 click，就可以写成下面这样：

```python
from click import click

@click.command()
@click.option('--count', default=1, help='Number of greetings.')
@click.option('--name', prompt='Your name',
              help='The person to greet.')
def hello(count, name):
    ...
```

在上面的示例中：

- 使用装饰器的方式，即定义了参数，又将之与处理逻辑绑定，这真是优雅。和 argparse、docopt 比起来，就少了一步绑定过程

- 使用 `click.command` 表示 hello 是对命令的处理

- 使用 `click.option` 来定义参数选项
	- 对于 `--count` 来说，使用 default 来指定默认值。而由于默认值是数字，进而暗示 --count 选项的类型为数字
	- 对于 `--name` 来说，使用 prompt 来指定未输入该选项时的提示语
	- 使用 `help` 来指定帮助信息

不论是装饰器的方式、还是各种默认行为，`click`都是像它的介绍所说的那样，让人尽可能少地编写代码，让整个过程变得快速而有趣。

## 代码梳理

使用 click 的方式非常简单，我们将上文的代码汇总下，以有一个更清晰的认识：

```python
# hello.py
import click

@click.command()
@click.option('--count', default=1, help='Number of greetings.')
@click.option('--name', prompt='Your name',
              help='The person to greet.')
def hello(count, name):
    """Simple program that greets NAME for a total of COUNT times."""
    for x in range(count):
        click.echo('Hello %s!' % name)

if __name__ == '__main__':
    hello()
```

若我们指定次数和名字：

```
$ python3 hello.py --count 2 --name Eric
Hello Eric!
Hello Eric!
```

若我们什么都不指定，则会提示输入名字，并默认输出一次：

```
$ python3 hello.py
Your name: Eric
Hello Eric!
```

我们还可以通过 --help 参数查看自动生成的帮助信息：

```
$ python test.py --help
Usage: hello.py [OPTIONS]

  Simple program that greets NAME for a total of COUNT times.

Options:
  --count INTEGER  Number of greetings.
  --name TEXT      The person to greet.
  --help           Show this message and exit.
```

## 小结

`click` 的思路非常简单，定义处理函数，通过它的装饰器来定义参数。使用装饰器的绝妙之处就在于把定义和绑定这两个步骤合为一个步骤，使得整个过程变得如丝般顺滑。

`click` 除了以 `Pythonic` 的方式让命令行程序的实现变得更加优雅和好用外，还提供了比 `argparse` 和 `docopt` 都要强大的功能。在接下来几节中，我们将会逐步揭开它的面纱。

<br/>

# 参数篇

<br/>

- [Python 命令行之旅：深入 click 之参数篇](https://www.cnblogs.com/xueweihan/p/11840729.html)

在概念上， click 把命令行分为 3 个组成：参数、选项和命令。

- 参数 就是跟在命令后的除选项外的内容，比如 `git add a.txt` 中的 `a.txt` 就是表示文件路径的参数
- 选项 就是以 `-` 或 `--` 开头的参数，比如 `-f`、`--file`
- 命令 就是命令行的初衷了，比如 `git` 就是命令，而 `git add` 中的 `add` 则是 `git` 的子命令

## 参数

### 基本参数

基本参数 就是通过位置里指定参数值。

比如，我们可以指定两个位置参数 `x` 和 `y` ，先添加的 `x` 位于第一个位置，后加入的 `y` 位于第二个位置。那么在命令行中输入 `1 2` 的时候，分别对应到的就是 `x` 和 `y`：

```python
import click
@click.command()
@click.argument('x')
@click.argument('y')
def hello(x, y):
    print(x, y)

if __name__ == '__main__':
    hello()
```

执行

```
$ python test.py 1 2
1 2
```

### 参数类型

参数类型 就是将参数值作为什么类型去解析，默认情况下是字符串类型。我们可以通过 `type` 入参来指定参数类型。

click 支持的参数类型多种多样：

- `str` / `click.STRING` 表示字符串类型，这也是默认类型
- `int` / `click.INT` 表示整型
- `float` / `click.FLOAT` 表示浮点型
- `bool` / `click.BOOL` 表示布尔型。很棒之处在于，它会识别表示真/假的字符。对于 `1`、`yes`、`y` 和 `true` 会转化为 `True`；`0`、`no`、`n` 和 `false` 会转化为 `False`
- `click.UUID` 表示 `UUID`，会自动将参数转换为 `uuid.UUID` 对象
- `click.FILE` 表示文件，会自动将参数转换为文件对象，并在命令行结束时自动关闭文件
- `click.PATH` 表示路径
- `click.Choice` 表示选择选项
- `click.IntRange` 表示范围选项

同 argparse 一样，click 也支持自定义类型，需要编写 click.ParamType 的子类，并重载 convert 方法。

官网提供了一个例子，实现了一个整数类型，除了普通整数之外，还接受十六进制和八进制数字， 并将它们转换为常规整数：

```python
class BasedIntParamType(click.ParamType):
    name = "integer"

    def convert(self, value, param, ctx):
        try:
            if value[:2].lower() == "0x":
                return int(value[2:], 16)
            elif value[:1] == "0":
                return int(value, 8)
            return int(value, 10)
        except TypeError:
            self.fail(
                "expected string for int() conversion, got "
                f"{value!r} of type {type(value).__name__}",
                param,
                ctx,
            )
        except ValueError:
            self.fail(f"{value!r} is not a valid integer", param, ctx)

BASED_INT = BasedIntParamType()
```

### 文件参数

在基本参数的基础上，通过指定参数类型，我们就能构建出各类参数。

文件参数 是非常常用的一类参数，通过 type=click.File 指定，它能正确处理所有 Python 版本的 unicode 和 字节，使得处理文件十分方便。

```python
@click.command()
@click.argument('input', type=click.File('rb'))  # 指定文件为二进制读
@click.argument('output', type=click.File('wb'))  # 指定文件为二进制写
def inout(input, output):
    while True:
        chunk = input.read(1024)  # 此时 input 为文件对象，每次读入 1024 字节
        if not chunk:
            break
        output.write(chunk)  # 此时 output 为文件对象，写入上步读入的内容
```

### 文件路径参数

文件路径参数 用来处理文件路径，可以对路径做是否存在等检查，通过 `type=click.Path` 指定。不论文件名是 unicode 还是字节类型，获取到的参数类型都是 unicode 类型。

```python
@click.command()
@click.argument('filename', type=click.Path(exists=True))  # 要求给定路径存在，否则报错
def hello(filename):
    click.echo(click.format_filename(filename))
```


如果文件名是以 `-` 开头，会被误认为是命令行选项，这个时候需要在参数前加上 `--` 和空格，比如

```
$ python hello.py -- -foo.txt
-foo.txt
```

### 选择项参数

选择项参数 用来限定参数内容，通过 `type=click.Choice` 指定。

比如，指定文件读取方式限制为 `read-only` 和 `read-write`：

```python
@click.command()
@click.argument('mode', type=click.Choice(['read-only', 'read-write']))
def hello(mode):
    click.echo(mode)
```

### 可变参数

可变参数 用来定义一个参数可以有多个值，且能通过 `nargs` 来定义值的个数，取得的参数的变量类型为元组。

若 `nargs=N`，`N`为一个数字，则要求该参数提供 `N` 个值。若 `N` 为 `-1` 则接受提供无数量限制的参数，如:

```python
@click.command()
@click.argument('foo', nargs=-1)
@click.argument('bar', nargs=1)
def hello(foo, bar):
    pass
```

ps： 只能有一个参数可以有多个值

举个例子

```python
import click


@click.command()
@click.argument('foo', nargs=-1)
@click.argument('bar', nargs=-1)
def hello(foo, bar):
    print(foo)
    print(bar)


if __name__ == '__main__':
    hello()
```

输出

```
$ python test.py 1 2 3 4 5 23 34 , 23
('1', '2', '3', '4', '5', '23', '34', ',')
23
```

如果要实现 `argparse` 中要求参数数量为 1 个或多个的功能，则指定 `nargs=-1` 且` required=True` 即可：

```python
@click.command()
@click.argument('foo', nargs=-1, required=True)
def hello(foo, bar):
    pass
```

### 从环境变量读取参数

通过在 click.argument 中指定 envvar，则可读取指定名称的环境变量作为参数值，比如：

```python
@click.command()
@click.argument('filename', envvar='FILENAME')
def hello(filename):
    print(filename)
```

执行如下命令查看效果：

```
$ FILENAME=hello.txt python3 hello.py
hello.txt
```

而在 `argparse` 中，则需要自己从环境变量中读取。

<br/>

# 选项篇

<br/>

## 选项

通过 `click.option` 可以给命令增加选项，并通过配置函数的参数来配置不同功能的选项。

### 给选项命名

`click.option` 中的命令规则可参考参数名称。它接受的前两个参数为长、短选项（顺序随意），其中：

- 长选项以 “--” 开头，比如 “--string-to-echo”
- 短选项以 “-” 开头，比如 “-s”

第三个参数为选项参数的名称，如果不指定，将会使用长选项的下划线形式名称：

```python
@click.command()
@click.option('-s', '--string-to-echo')
def echo(string_to_echo):
    click.echo(string_to_echo)
```

显示指定为 string

```python
@click.command()
@click.option('-s', '--string-to-echo', 'string')
def echo(string):
    click.echo(string)
```

### 基本值选项

值选项是非常常用的选项，它接受一个值。如果在命令行中提供了值选项，则需要提供对应的值；反之则使用默认值。若没在 `click.option` 中指定默认值，则默认值为 `None`，且该选项的类型为 `STRING`；反之，则选项类型为默认值的类型。

比如，提供默认值为 1，则选项类型为 `INT`：

```python
@click.command()
@click.option('--n', default=1)
def dots(n):
    click.echo('.' * n)
```

如果要求选项为必填，则可指定 `click.option` 的 `required=True`：

```python
@click.command()
@click.option('--n', required=True, type=int)
def dots(n):
    click.echo('.' * n)
```

如果选项名称和 Python 中的关键字冲突，则可以显式的指定选项名称。比如将 `--from` 的名称设置为 `from_`：

```python
@click.command()
@click.option('--from', '-f', 'from_')
@click.option('--to', '-t')
def reserved_param_name(from_, to):
    click.echo(f'from {from_} to {to}')
```

如果要在帮助中显式默认值，则可指定 `click.option` 的 `show_default=True`：

```python
@click.command()
@click.option('--n', default=1, show_default=True)
def dots(n):
    click.echo('.' * n)
```

在命令行中调用则有：

```
$ dots --help
Usage: dots [OPTIONS]

Options:
  --n INTEGER  [default: 1]
  --help       Show this message and exit.
```

