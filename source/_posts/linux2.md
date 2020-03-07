---
title: linux 的 crontab
date: 2019-11-24 19:45:21
categories:
- linux
tags:
- linux
- crontab
---
这个蛮有用的，定时任务。

<!-- more -->

在正式看这篇博文之前，请把我之前的博文看一遍。

[python-crontab](https://benpaodewoniu.github.io/2019/10/27/python61/)

<br/>

# 查看 crontab

<br/>

	crontab -l

<br/>

# 修改

<br/>

	crontab -e

上面的那条命令直接进文件进行修改。

按照 crontab 的格式进行编写就好。

<br/>

# 注意点

<br/>

当我们用定时任务的时候，很多情况下，我们自己执行 python 就可以，但是使用定时任务执行就不可以，我所遇见的情况如下，给大家解释一下。

当然，有一些东西我们在我上面的博文中也给出了例子，大家可以先去看那一篇。

## 疑惑点1

有时候，我们在本地可以运行，但是，部署到服务器上就不可以。

比如，我们建立了一个文件 config.py

然后，我们在项目的某一处引用了它

	import config.py

我们在本地无论是使用命令行还是pycharm都可以运行，但是部署到服务器上，就有可能报 没有找到这个模块的错误。

这个，问题，我至今没有什么明确的答案，中文搜索不到相关的问题，但是，我猜想，是因为 import 不知道路径，所以，我们要告知它路径，比如：

这个 config.py 文件时在 /root/eosvoter/ 的路径下，所以，我们将这个路径添加到进去

在 import config 之前，我们使用下面的命令：

	sys.path.append('/root/eosvoter/')

<br/>

# 执行时出错

<br/>

比如，我再本地执行没有错误，但是在定时任务上执行 python /root/*.py 就会出错。

如果，你看过我上面的那个博客，可能会对这个问题有一定的印象。

这是因为 crontab 太笨了，所以，你要把你的真实环境尽可能的匹配。

OK，我们在定时任务下这样写：

	python /root/eosvoter/test.py

看似是制定了具体的文件，没毛病，但是，定时任务根本执行不了，这是因为，我们从来没有这个执行过。

我们的操作是这样的

第一步

	cd /root/eosvoter/

第二部

	python test.py

所以，我们一定要严格复制自己的操作，在 crontab 我们可以这样修改

	cd /root/eosvoter/;python test.py

而下面的这两个命令可能都会导致错误

	cd /root/eosvoter/;python ./test.py
	cd /root/eosvoter/;python /root/eosvoter/test.py

而，究竟定时器的任务究竟能不能执行，你可以跳到任意一个目录下

复制定时器中的 command line 试一下，看是否可以。

{% img /images/linux/2_0.png %}

当然，关于重定向输入到文件中，你可以参考我下面的博文。

