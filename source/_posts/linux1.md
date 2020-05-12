---
title: 在 linux 重定向
date: 2019-11-24 19:40:39
categories:
- [linux,基础]
tags:
- linux
- 重定向
---
这个非常有用，也是 linux 的优势之处。

特别提示，该命令都运行在 ubuntu 上。

<!-- more -->

输出即把相关对象通过输出设备（显示器等）显示出来，输出又分正确输出和错误输出

一般情况下标准输出设备为显示器，标准输入设备为键盘。

![](/images/linux/1_0.png)

linux中用0代表标准输入，1代表标准正确输出，2代表标准错误输出。

输出重定向：正常输出是把内容输出到显示器上，而输出重定向是把内容输出到文件中

![](/images/linux/1_1.png)

例如：ifconfig > test.log 即把ifconfig执行显示的内容写入test.log.当前页面不再显示执行结果。

注意：错误输出重定向>/>>后边没有空格

![](/images/linux/1_2.png)

命令 >> 文件 2>&1 和 命令 &>>文件 两个命令作用相同

系统有个常见用法 ls &>/dev/null 正确输出或错误输出结果都不要。（null可以理解为黑洞或垃圾站）

命令>>文件1 2>>文件2 正确日志和错误日志分开保存

例：nohup java -jar /usr/sbin/lb-agent.jar >>/var/log/lb-agent.log 2>&1 &

将jar执行的结果写入到lb-agent.log中

在命令最后加上&符号，表示让这个进程到后台去执行，这样立刻返回到提示符状态，我们可以接着做下面的事。如：command &。

这种“后台”进程在shell一直打开的情况下是没有问题的，如果我们关了shell窗口甚至退出ssh登录或vnc登录，那么进程自动就结束了。所以如果想退出窗口乃至退出登录仍然保持程序运行，再加上nohup，形如：nohup command 1>output 2>&1 &
