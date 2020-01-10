---
title: pycharm 配置 anaconda 环境
date: 2019-01-21 10:39:20
categories:
- anaconda
tags:
- anaconda
---
昨天为了配置环境，搞到 1 点多，几近崩溃，于是，今天我又来到实验室，然后搞了 2 分钟，就他妈的 OK 了，我是一万个不服啊！！！！
<!-- more -->
## 参考资料
[Pycharm 2018.1.1 使用anaconda 3中已创建的环境](https://blog.csdn.net/anjingshen/article/details/80038316)
## 问题描述
### 直接使用 anaconda 的环境
我是按照最原始的步骤来配置的。
{% img /images/python/48_0.png %}
{% img /images/python/48_1.png %}
#### 使用虚拟环境来配置 anaconda
{% img /images/python/48_2.png %}
我在网上遍地寻找都没有解决方案，直到我用了百度。。。2333333
#### 解决方案
就是上面参考资料的解决方案。
{% img /images/python/48_3.png %}
然后就是定位到anaconda中已有环境的python.exe所在目录了
假设环境名称是tensorflow，一般路径是：
C:\Users\[username]\AppData\Local\conda\conda\envs\tensorflow\python.exe
{% img /images/python/48_4.png %}
### 在 anaconda 中下载了第三方库，但是 pycharm 却没有显示
按照上面的步骤，正确的配置好路径之后，我们下载 numpy 
在 conda 的界面上，我们输入

	conda list
	
可以发现上面有 numpy
但是，我们 run 相关代码，就会发现没有 numpy 。
如果打开 setting 找到 interpreter ，就会发现我们的那个环境下的第三方包是没有 numpy 的。
{% img /images/python/48_5.png %}
在查询了很多资料之后，按照下面的步骤我成功的解决了问题，但是，我不知道哪一步是有用的。
#### 删除所有的编辑器引用
{% img /images/python/48_6.png %}
{% img /images/python/48_7.png %}
选择环境，然后按照 delete 删除所有的环境。
再重新引用环境，也就是 add 之类的配置选项。
我们再重新看 interpreter 下的第三方包的时候，就会发现 numpy 出现了
我们在 pycharm 下写代码，也会发现 numpy 在环境中，具体体现在代码可以自动补全。
然而，我们运行的时候，还是会出现没有这个包，或者其它错误。
#### 在 anaconda 的命令行中运行
对于上面的问题，我采用的是，打开 prompt 然后进入对应的虚拟环境。
在虚拟环境下按照命令行方式运行
{% img /images/python/48_8.png %}
代码正确运行，所以，我以后的策略就是，在 pycharm 中编辑代码，但是在命令行中运行代码。








