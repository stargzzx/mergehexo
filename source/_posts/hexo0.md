---
title: hexo 所遇到的错误
date: 2018-06-10 18:04:23
categories:
- hexo
tags:
- hexo
- error
- tutorial
---
这里记录了我所遇到的hexo的异常。
 <!-- more -->
[Hexo搭建Github-Pages博客填坑教程](https://www.jianshu.com/p/35e197cb1273)
## 1 TaskCanceledException异常
如下面代码所示（可能微有出入，但是也差不多）
{% codeblock %}
fatal: TaskCanceledException encountered.

bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': No error
FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io
cs/troubleshooting.html
Error: fatal: TaskCanceledException encountered.

bash: /dev/tty: No such device or address
error: failed to execute prompt script (exit code 1)
fatal: could not read Username for 'https://github.com': No error

    at ChildProcess.<anonymous> (C:\Users\Administrator\benpaodewoniu.github.
node_modules\hexo-util\lib\spawn.js:37:17)
    at emitTwo (events.js:106:13)
    at ChildProcess.emit (events.js:191:7)
    at ChildProcess.cp.emit (C:\Users\Administrator\benpaodewoniu.github.io\n
_modules\cross-spawn\lib\enoent.js:40:29)
    at maybeClose (internal/child_process.js:877:16)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:226:5)
 {% endcodeblock %}
### 解决方案
我尝试修改了 _config.yml 中的
{% codeblock %}
deploy:
  type: git
  repo: https://github.com/benpaodewoniu/benpaodewoniu.github.io.git
  name: licong
  email: 1506316219@qq.com
  branch: master
 {% endcodeblock %}
上面是原来的样子，我经过寻找网上方案，改变原来的
{% codeblock %}
repo: https://github.com/benpaodewoniu/benpaodewoniu.github.io.git
{% endcodeblock %}
进行如下尝试：
{% codeblock %}

#repo: ssh://git@github.com/benpaodewoniu/benpaodewoniu.github.io.git
#repository: ssh://git@github.com/benpaodewoniu/benpaodewoniu.github.io.git
#repo: ssh://git@github.com:benpaodewoniu/benpaodewoniu.github.io.git
#repository: ssh://git@github.com:benpaodewoniu/benpaodewoniu.github.io.git
#repo: git@github.com:benpaodewoniu/benpaodewoniu.github.io.git
#repository: git@github.com:benpaodewoniu/benpaodewoniu.github.io.git
#repo: git@github.com/benpaodewoniu/benpaodewoniu.github.io.git
#repository: git@github.com/benpaodewoniu/benpaodewoniu.github.io.git

{% endcodeblock %}
奋战了一个小时，全部失败，我万念俱灰，尝试另一个博主的方法。将其改为原来的配置，即如下
{% codeblock %}

deploy:
  type: git
  #repo: ssh://git@github.com/benpaodewoniu/benpaodewoniu.github.io.git
  repo: https://github.com/benpaodewoniu/benpaodewoniu.github.io.git
  #repository: git@github.com:benpaodewoniu/benpaodewoniu.github.io.git
  name: licong
  email: 1506316219@qq.com
  branch: master
  
 {% endcodeblock %}
然后趁电脑不注意，重新运行
{% codeblock %}

hexo clean
hexo g
hexo d

{% endcodeblock %}
最后，竟然 TMD 成功了。这个文件，我又提交了一次，发现又出现了这个情况，我又回想起上面成功的例子。
在我改回原来的配制的时候，我提交的过程，有弹出一个小框，叫我输入用户名和密码。
当然，以前我也遇到过这个情况，但第一次我用的是邮箱登录，没有成功，而我用用户名登录，即 benpaodewoniu 成功了。
我决定尝试修改配置中的名字在尝试一下。
我 TMD 的修改配置如下：
{% codeblock %}

deploy:
  type: git
  repo: https://github.com/benpaodewoniu/benpaodewoniu.github.io.git
  name: benpaodewoniu
  email: 1506316219@qq.com
  branch: master

{% endcodeblock %}
OK，在后续的过程中，有的成功，有的失败，我最终也没有搞清楚，所以，我的解决方案是，如果提交不成功，那就多提交几次，如果多提交几次不成功，那就隔段时间提交。
就这样，我 TM 快疯了。
## 修改上传缓存
出现如下的错误

	Error: fatal: the remote end hung up unexpectedly
	fatal: the remote end hung up unexpectedly
	error: RPC failed; curl 56 OpenSSL SSL_read: SSL_ERROR_SYSCALL, errno 10054
	Everything up-to-date

[参考资料](https://blog.csdn.net/u012306714/article/details/52571596)
[参考资料2](http://www.mamicode.com/info-detail-2441465.html)
[参考资料3](http://www.cnblogs.com/wang715100018066/p/10372600.html)
一开始我打算修改 git 总的 config 但是，找了半天没找到。
而且，我直接执行 

	git config http.postBuffer 524288000
	
出现如下的错误

	fatal: Not a git repository
	
所以，我打算为项目单独创立一个 config 文件。
进入我的 hexo 文件夹，比如我的路径是

	e://site/blog
	
执行

	git init
	
然后执行

	git config http.postBuffer 524288000
	
最后上传就没有问题了，建议执行这些命令的时候要注意备份。毕竟数据无价。

