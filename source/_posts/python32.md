---
title: python argparse 模块
date: 2018-12-03 20:45:01
categories:
- python
tags:
- argparse模块
- python 模块
- python
- tutorial
---
这是python 的 argparse 模块。

<!-- more -->

[原文链接](https://www.jianshu.com/p/fef2d215b91d)

有一个小小的前提，假设所有的代码都写在 test.py 中。

## argparse介绍

是python的一个命令行解析包，编写可读性非常好的程序

即在命令行中输入参数，反正看上去挺装逼的。所以说，嘿嘿嘿。

## 例子

### 命令行中的 hello world

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.parse_args()
	# 这个时候我们并没有输入参数的请求
	# 但是我们依然可以输入一个默认的参数，及 -h
{% endcodeblock %}

cmd 中输入 python test.py -h

输出

	usage: test.py [-h]
	optional arguments:
		-h, --help  show this help message and exit

### 指定参数

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("echo")
args = parser.parse_args()
print(args.echo)
{% endcodeblock %}

输出

	123
	
### 对设置的参数进行详细的说明

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("echo", help="echo the string you use here")
args = parser.parse_args()
print(args.echo)
{% endcodeblock %}

输入
 
	python test.py -h
	
cmd 中显示：

	usage: test.py [-h] echo
	positional arguments:
		echo        echo the string you use here
	optional arguments:
		-h, --help  show this help message and exit
 
### 输入指定类型的参数

我们在 cmd 中输入的参数，都会被转化为 string 类型，但有时我们需要 int 类型。

一般有两种方法

第一种是获得参数后在参数内部进行转化

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", help="display a square of a given number")
args = parser.parse_args()
print int(args.square)**2
{% endcodeblock %}

第二种是在add_argument()方法中用关键字type指定输入类型，即可解决

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", help="display a square of a given number", type=int)
args = parser.parse_args()
print(args.square ** 2)
	# 15129
{% endcodeblock %}

### 可选参数

设置可选参数后，程序可以不输入该参数值运行

如果程序运行时要赋值可选参数，必须先指定该可选参数

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--verbosity", help="increase output verbosity")
args = parser.parse_args()
if args.verbosity:
	print ("verbosity turned on")
{% endcodeblock %}

输入

	python test.py
		# 没有错误
		
输入

	python test.py --verbosity 123
	verbosity turned on

### 指定参数位布尔值

默认可选参数会被赋值为str，而在实际情况中，很多时候，可选参数是作为一个标识而不是一个确切的值，仅需要确定是true或false即可，可以指定关键字action，赋值为"store_true"

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--verbosity", help="increase output verbosity", action="store_true")
args = parser.parse_args()
if args.verbosity:
	print("verbosity turned on")
{% endcodeblock %}

输入

	python test.py --verbosity
		# verbosity turned on
		
这个命令可能有一点让人疑惑，我来详细的解释一下吧。我们如果直接在命令行中写出 --verbosity 参数，那么这个值就被赋予 1 也就是 True，如果没有这个参数那么就是 0 也就是 False

### 增加快捷方式

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("-v", "--verbose", help="increase verbosity", action="store_true")
args = parser.parse_args()
if args.verbose:
	print("verbosity turned on")
{% endcodeblock %}

输入

	python test.py -v
		# verbosity turned on
		
### 结合使用位置参数和可选参数

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int, help="display a square of a given number")
parser.add_argument("-v", "--verbose", action="store_true", help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbose:
	print( "the square of {} equals {}".format(args.square, answer))
else:
	print (answer)
{% endcodeblock %}

注意，参数顺序没有关系

输入

	python test.py -v 4
		# the square of 4 equals 16
	python test.py  4
		# 16
		
{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int, help="display a square of a given number")
parser.add_argument("-v", "--verbosity", type=int, help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbosity == 2:
	print("the square of {} equals {}".format(args.square, answer))
elif args.verbosity == 1:
	print("{}^2 == {}".format(args.square, answer))
else:
	print(answer)
{% endcodeblock %}

输入

	python test.py  5 -v 2
		# the square of 5 equals 25
		

### 指定参数范围

	parser.add_argument("-v", "--verbosity", type=int, choices=[0,1,2], help="increase output verbosity")

### action 属性的运用

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int, help="display a square of a given number")
parser.add_argument("-v", "--verbosity", action="count", help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbosity == 2:
	print("the square of {} equals {}".format(args.square, answer))
elif args.verbosity == 1:
	print("{}^2 == {}".format(args.square, answer))
elif args.verbosity == 3:
    print("test")
else:
	print(answer)
{% endcodeblock %}

这个 count 的作用是，额，还是通过例子来说明吧

输入

	python test.py  5
		# 25
	python test.py  5 -v
		# 5^2 == 25
	python test.py  5 -vv
		# the square of 5 equals 25
	python test.py  5 -vvv
		# test
	python test.py  5 -v -v -v
		# test
	python test.py  5 -v -v
		# the square of 5 equals 25
		
### 关键字default来指定选项默认的值

	parser.add_argument("-v", "--verbosity", default=2, action="count", help="increase output verbosity")
	
### 多个位置参数

{% codeblock %}
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
parser.add_argument("-v", "--verbosity", action="count", default=0)
args = parser.parse_args()
answer = args.x**args.y
if args.verbosity >= 2:
	print( "{} to the power {} equals {}".format(args.x, args.y, answer))
elif args.verbosity >= 1:
	print ("{}^{} == {}".format(args.x, args.y, answer))
else:
	print (answer)
{% endcodeblock %}

输入

	python test.py  5 2 -vv
		# 5 to the power 2 equals 25
		
### 官方源码

下面来一个官方源码，供大家参考

{% codeblock %}
if __name__ == '__main__':
    script_start_time = time.time()
 
    parser = argparse.ArgumentParser(description='Classification example - DIGITS')
 
    ### Positional arguments
 
    parser.add_argument('caffemodel',   help='Path to a .caffemodel')
    parser.add_argument('deploy_file',  help='Path to the deploy file')
    parser.add_argument('image',        help='Path to an image')
 
    ### Optional arguments
 
    parser.add_argument('-m', '--mean',
            help='Path to a mean file (*.npy)')
    parser.add_argument('-l', '--labels',
            help='Path to a labels file')
    parser.add_argument('--nogpu',
            action='store_true',
            help="Don't use the GPU")
 
    args = vars(parser.parse_args())
 
    image_files = [args['image']]
 
    classify(args['caffemodel'], args['deploy_file'], image_files,
            args['mean'], args['labels'], not args['nogpu'])
 
    print 'Script took %s seconds.' % (time.time() - script_start_time,)
{% endcodeblock %}


