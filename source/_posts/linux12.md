---
title: 什么是守护进程
date: 2020-05-07 22:02:19
categories:
- linux
tags:
- linux
- 守护进程
---
早就听闻守护进程了，尤其是，之前瑞哥他们经常说，开个守护进程。

我在旁边只能黑人问号？？？

<!-- more -->

"守护进程"（daemon）就是一直在后台运行的进程（daemon）。

本文介绍如何将一个 Web 应用，启动为守护进程。

<br/>

# 参考资料

<br/>

- [Linux 守护进程的启动方法](http://www.ruanyifeng.com/blog/2016/02/linux-daemon.html)

<br/>

# 问题的由来

<br/>

Web应用写好后，下一件事就是启动，让它一直在后台运行。

这并不容易。举例来说，下面是一个最简单的Node应用`server.js`，只有6行。

```js
var http = require('http');

http.createServer(function(req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World');
}).listen(5000);
```

你在命令行下启动它。


	$ node server.js

看上去一切正常，所有人都能快乐地访问 5000 端口了。但是，一旦你退出命令行窗口，这个应用就一起退出了，无法访问了。

怎么才能让它变成系统的守护进程（daemon），成为一种服务（service），一直在那里运行呢？

<br/>

# 前台任务与后台任务

<br/>

上面这样启动的脚本，称为"前台任务"（foreground job）。它会独占命令行窗口，只有运行完了或者手动中止，才能执行其他命令。

变成守护进程的第一步，就是把它改成"后台任务"（background job）。

	$ node server.js &

只要在命令的尾部加上符号`&`，启动的进程就会成为"后台任务"。如果要让正在运行的"前台任务"变为"后台任务"，可以先按`ctrl + z`，然后执行`bg`命令（让最近一个暂停的"后台任务"继续执行）。

ps: 注意，是让一个前台任务变成后台任务。

	node server.js
		ctrl + Z
			输出 zsh: suspended  node server.js
	ps aux | grep node
		14055   0.0  0.2  4529080  17444 s002  T    10:15下午   0:00.04 node server.js
	bg
	ps aux | grep node
		14055   0.0  0.2  4568168  17036 s002  S    10:15下午   0:00.08 node server.js

"后台任务"有两个特点。

1. 继承当前 session （对话）的标准输出（stdout）和标准错误（stderr）。因此，后台任务的所有输出依然会同步地在命令行下显示。

2. 不再继承当前 session 的标准输入（stdin）。你无法向这个任务输入指令了。如果它试图读取标准输入，就会暂停执行（halt）。

可以看到，"后台任务"与"前台任务"的本质区别只有一个：是否继承标准输入。所以，执行后台任务的同时，用户还可以输入其他命令。

<br/>

# SIGHUP信号

<br/>

变为"后台任务"后，一个进程是否就成为了守护进程呢？或者说，用户退出 session 以后，"后台任务"是否还会继续执行？

Linux系统是这样设计的。

1. 用户准备退出 session
2. 系统向该 session 发出SIGHUP信号
3. session 将SIGHUP信号发给所有子进程
4. 子进程收到SIGHUP信号后，自动退出

上面的流程解释了，为什么"前台任务"会随着 session 的退出而退出：因为它收到了`SIGHUP`信号。

那么，"后台任务"是否也会收到`SIGHUP`信号？

这由 Shell 的`huponexit`参数决定的。

	shopt | grep huponexit

ps:上面的命令，我在 mac 执行失败，也没有找到 `huponexit` 这个有效的信息。

执行上面的命令，就会看到`huponexit`参数的值。

大多数Linux系统，这个参数默认关闭（`off`）。因此，session 退出的时候，不会把`SIGHUP`信号发给"后台任务"。所以，一般来说，"后台任务"不会随着 session 一起退出。

<br/>

# disown 命令

<br/>

通过"后台任务"启动"守护进程"并不保险，因为有的系统的`huponexit`参数可能是打开的（`on`）。

更保险的方法是使用`disown`命令。它可以将指定任务从"后台任务"列表（`jobs`命令的返回结果）之中移除。一个"后台任务"只要不在这个列表之中，session 就肯定不会向它发出`SIGHUP`信号。

	node server.js &
	disown

执行上面的命令以后，server.js进程就被移出了"后台任务"列表。你可以执行jobs命令验证，输出结果里面，不会有这个进程。

ps： jobs 仅对当前所在的对话有效

也就是，你开启了两个终端，在其中一个终端运行

	node server.js &
	jobs
		输出 [1]  + running    node server.js
	然后切换到另一个终端
	jobs
		没有输出

另外，直接用 disown 也不会直接将 `node server.js` 从 `jobs` 移除掉。

如果原来的里面有其他的后台任务，那么会先把最早的后台移除，比如

	jobs
		[1]    suspended  git pull origin master
		[2]    node server.js
	disown
		会先把 1 移除

disown的用法如下。

	# 移出最近一个正在执行的后台任务
	disown

	# 移出所有正在执行的后台任务
	disown -r

	# 移出所有后台任务
	disown -a

	# 不移出后台任务，但是让它们不会收到SIGHUP信号
	disown -h

	# 根据jobId，移出指定的后台任务
	disown %2
	disown -h %2

<br/>

# 标准 I/O

<br/>

使用`disown`命令之后，还有一个问题。那就是，退出 session 以后，如果后台进程与标准I/O有交互，它还是会挂掉。

还是以上面的脚本为例，现在加入一行。

```js
var http = require('http');

http.createServer(function(req, res) {
  console.log('server starts...'); // 加入此行
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World');
}).listen(5000);
```

启动上面的脚本，然后再执行disown命令。

	node server.js &
	disown

接着，你退出 session，访问5000端口，就会发现连不上。

这是因为"后台任务"的标准 I/O 继承自当前 session，`disown`命令并没有改变这一点。一旦"后台任务"读写标准 I/O，就会发现它已经不存在了，所以就报错终止执行。

为了解决这个问题，需要对"后台任务"的标准 I/O 进行重定向。

	node server.js > stdout.txt 2> stderr.txt < /dev/null &
	disown

上面这样执行，基本上就没有问题了。

<br/>

# nohup 命令

<br/>

还有比`disown`更方便的命令，就是`nohup`。


	$ nohup node server.js &

`nohup`命令对`server.js`进程做了三件事。

- 阻止SIGHUP信号发到这个进程。
- 关闭标准输入。该进程不再能够接收任何输入，即使运行在前台。
- 重定向标准输出和标准错误到文件nohup.out。

也就是说，nohup命令实际上将子进程与它所在的 session 分离了。

注意，nohup命令不会自动把进程变为"后台任务"，所以必须加上`&`符号。

ps: 原文中还有另外的思路，感兴趣的同学可以点击原文查看