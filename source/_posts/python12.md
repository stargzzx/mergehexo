---
title: python 异常
date: 2018-06-08 14:20:38
categories:
- python
- 基础
tags:
- 异常
- python
- 错误
---
这里是 python 的异常总结

<!-- more -->

## 参考资料

[python 自定义异常和异常捕捉](https://blog.csdn.net/flyingshuai/article/details/73482177)

## 异常
```python
try:
	检测范围

except Exception[as reason]:
	出现异常(Exception)后的处理代码

	try:
		code
	except OSError:
		code
	try:
		code
	except OSError as reason:		reason是一个变量，保存错误的原因
		code
		print(str(reason))


检测到异常，会直接跳到异常处理信息，后面的代码不会运行

try:
	code
excrpt Exception:
	code

finally:
	code 无论怎样都会被执行的代码

raise 语句，自主引发异常
	raise Exception
		raise ZeroDivisionError("除数为零的异常")
```

![](/images/python/12_0.JPG)

![](/images/python/12_1.JPG)

## 异常参数输出：

```python
try:
    testRaise()
except PreconditionsException as e: #python3的写法，必须用as
									# python2 是另一种写法
									# except PreconditionsException,e:
    print (e)
```

## 自定义异常

自定义异常，只需自定义异常类继承父类Exception。在自定义异常类中，重写父类init方法。

```python
class DatabaseException(Exception):
    def __init__(self,err='数据库错误'):
        Exception.__init__(self,err)

class PreconditionsException(DatabaseException):
    def __init__(self,err='PreconditionsErr'):
        DatabaseException.__init__(self,err)

def testRaise():
    raise PreconditionsException()

try:
    testRaise()
except PreconditionsException as e:
    print (e)
		# PreconditionsErr
```

注意：PreconditonsException又是DatabaseException的子类。 

所以如果，raise PreconditionException的话，用两个异常类都可以捕捉。 

但是, 如果是raise DatabaseException, 用PreconditonsException是捕捉不到的。

## 具体样例

### IndentationError:unindent does not match any outer indentation level

对于此错误，最常见的原因是，的确没有缩进。根据错误提示的行数，去代码中看了下，看起来没有什么问题呀，都有缩进，而且语法也没有错误呀。

仔细研究了下代码，发现真的看不出什么问题，突然想到了，把当前python脚本的所有字符（包括空格和tab字符）都显示出来看看到底有没有缩进或者是其他特殊的字符。

选用Notepad++：

当前用的文本编辑器Notepad++，有个设置，可以显示所有的字符的。 

在： 

视图 –> 显示符号 –> 显示空格与制表符 

这样就可以看到我们Python代码的到底有没有该缩进的都缩进了。

问题大都是本来是 tab 却变成了 空格，常见于 copy 时候的自动转换之类的。这问题的解决要借助 Notepad++ 的空格视图来查找，并且替换。
