---
title: linux 后台运行程序
date: 2020-01-01 21:31:19
categories:
- linux
tags:
- linux
- 进程
---
如果我们想保持一个命令后台运行的话，你就需要读这个文章。

<!-- more -->

<br/>

# &

<br/>

在Linux中，当在前台运行某个作业时，终端被该作业占据；而在后台运行作业时，它不会占据终端。可以使用&命令把作业放到后台执行。实际上，这样是将命令放入到一个作业队列中了。

<br/>

# 后台运行命令

<br/>

有两种方式：

	command & ： 后台运行，你关掉终端会停止运行

	nohup command & ： 后台运行，你关掉终端也会继续运行

<br/>

# 后台运行脚本

<br/>

## 情况 1

1.执行脚本test.sh:./test.sh

2.中断脚本test.sh：ctrl+c

3.在1的基础上将运行中的test.sh，切换到后台并暂停：ctrl+z

4.执行ctrl+z后，test.sh在后台是暂停状态（stopped）,使用命令：bg number让其在后台开始运行（“number”是使用jobs命令查到的 [ ]中的数字，不是pid）

## 情况2

1.直接在后台运行脚本test.sh:./test.sh &

2.查看当前shell环境中已启动的任务情况：jobs

3.将test.sh切换到前台运行：fg %number（”number”为使用jobs命令查看到的 [ ] 中的数字，不是pid）

4.中断后台运行的test.sh脚本：先fg %number切换到前台，再ctrl+c；或是直接kill %number

以上两种在后台运行test.sh的方法，当遇到退出当前shell终端时，后台运行的test.sh也就结束了。这是因为以上两种方法使得test.sh在后台运行时，运行test.sh进程的父进程是当前shell终端进程，关闭当前shell终端时，父进程退出，会发送hangup信号给所有子进程，子进程收到hangup以后也会退出。所以要想退出当前shell终端时test.sh继续运行，则需要使用nohup忽略hangup信号。

## 不会中断的情况

1.不中断的在后台运行test.sh：nohup ./test.sh &（test.sh的打印信息会输出到当前目录下的nohup.out中）

2.使用jobs可看到test.sh处于running状态

3.使用ps -ef |grep test.sh可查看到正在运行的test.sh脚本进程

4.退出当前shell终端，再重新打开，使用jobs看不到正在运行的test.sh，但使用ps -ef可以看到

## 另一个不会中断的版本

在后台不中断的运行test.sh，可以使用nohup忽略hangup信号，或者使用setsid将其父进程改为init进程（进程号为1）

1.不中断的在后台运行test.sh另一个命令：setsid ./test.sh &

2.使用ps -ef |grep test.sh可看到test.sh进程的父进程id为1

<br/>

# 扩展

<br/>

查看当前后台运行的命令

有两个命令可以用，jobs和ps,区别是jobs用于查看当前终端后台运行的任务，换了终端就看不到了。而ps命令用于查看瞬间进程的动态，可以看到别的终端运行的后台进程。

## jobs

jobs -l选项可显示当前终端所有任务的PID，jobs的状态可以是running，stopped，Terminated。+ 号表示当前任务，- 号表示后一个任务。

## 前后台进程的切换与控制

### fg命令

功能：将后台中的命令调至前台继续运行

如果后台中有多个命令，可以先用jobs查看jobnun，然后用 fg %jobnum 将选中的命令调出。%jobnumber是通过jobs命令查到的后台正在执行的命令的序号(不是pid)

### Ctrl + z 命令

功能：将一个正在前台执行的命令放到后台，并且处于暂停状态

### bg命令

功能：将一个在后台暂停的命令，变成在后台继续执行

如果后台中有多个命令，可以先用jobs查看jobnum，然后用 bg %jobnum 将选中的命令调出继续执行。%jobnumber是通过jobs命令查到的后台正在执行的命令的序号(不是pid)
