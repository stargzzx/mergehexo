---
title: linux | 语法
date: 2020-03-16 17:53:42
categories:
- linux
tags:
- linux
---
掌握 linux 语法是写系统脚本和提升技术栈的必经之路。

因为内容较少，就放在一个文件里面了。

<!-- more -->

<br/>

# 参考资料

<br/>

[Shell 教程](https://www.runoob.com/linux/linux-shell.html)

<br/>

# Shell 教程

<br/>

Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

Ken Thompson 的 sh 是第一种 Unix Shell，Windows Explorer 是一个典型的图形界面 Shell。

## shell 环境

Shell 编程跟 JavaScript、php 编程一样，只要有一个能编写代码的文本编辑器和一个能解释执行的脚本解释器就可以了。

Linux 的 Shell 种类众多，常见的有：

- Bourne Shell（/usr/bin/sh或/bin/sh）
- Bourne Again Shell（/bin/bash）
- C Shell（/usr/bin/csh）
- K Shell（/usr/bin/ksh）
- Shell for Root（/sbin/sh）

本教程关注的是 Bash，也就是 Bourne Again Shell，由于易用和免费，Bash 在日常工作中被广泛使用。同时，Bash 也是大多数Linux 系统默认的 Shell。

在一般情况下，人们并不区分 Bourne Shell 和 Bourne Again Shell，所以，像 #!/bin/sh，它同样也可以改为 #!/bin/bash。

\#! 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。

## 查询可用的 shell 环境

	cat /etc/shells 

输出

	/bin/sh
	/bin/bash
	/sbin/nologin

## 查看正在使用的 shell 环境

	echo $SHELL

## 第一个shell脚本

打开文本编辑器(可以使用 vi/vim 命令来创建文件)，新建一个文件 test.sh，扩展名为 sh（sh代表shell），扩展名并不影响脚本执行，见名知意就好，如果你用 php 写 shell 脚本，扩展名就用 php 好了。

输入一些代码，第一行一般是这样：

```shell
#!/bin/bash
echo "Hello World !"
```

\#! 是一个约定的标记，它告诉系统这个脚本需要什么解释器来执行，即使用哪一种 Shell。

echo 命令用于向窗口输出文本。

## 运行 Shell 脚本有两种方法：

### 作为可执行程序

将上面的代码保存为 test.sh，并 cd 到相应目录：

	chmod +x ./test.sh  #使脚本具有执行权限
	./test.sh  #执行脚本

注意，一定要写成 ./test.sh，而不是 test.sh，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

## 作为解释器参数

这种运行方式是，直接运行解释器，其参数就是 shell 脚本的文件名，如：

	/bin/sh test.sh
	/bin/php test.php

这种方式运行的脚本，不需要在第一行指定解释器信息，写了也没用。

<br/>

# Shell 变量

<br/>

## 定义变量

定义变量时，变量名不加美元符号（$，PHP语言中变量需要），如：

	your_name="runoob.com"

注意，变量名和等号之间不能有空格，这可能和你熟悉的所有编程语言都不一样。同时，变量名的命名须遵循如下规则：

- 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 中间不能有空格，可以使用下划线（_）。
- 不能使用标点符号。
- 不能使用bash里的关键字（可用help命令查看保留关键字）。

除了显式地直接赋值，还可以用语句给变量赋值，如：

	for file in `ls /etc`
	或
	for file in $(ls /etc)

以上语句将 /etc 下目录的文件名循环出来。

## 使用变量

使用一个定义过的变量，只要在变量名前面加美元符号即可，如：

	your_name="qinjx"
	echo $your_name
	echo ${your_name}

变量名外面的花括号是可选的，加不加都行，加花括号是为了帮助解释器识别变量的边界，比如下面这种情况：

	for skill in Ada Coffe Action Java; do
	    echo "I am good at ${skill}Script"
	done

如果不给skill变量加花括号，写成echo "I am good at $skillScript"，解释器就会把$skillScript当成一个变量（其值为空），代码执行结果就不是我们期望的样子了。

推荐给所有变量加上花括号，这是个好的编程习惯。

