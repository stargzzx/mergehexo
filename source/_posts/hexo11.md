---
title: 错误的重新安装 git 使得 hexo d 无效
date: 2019-12-07 15:55:56
categories:
- hexo
tags:
- hexo
---
今天安装 docker 的时候，其有一个选项是安装 git ，但是没有看清，默认安装，所以使得原有的git 被替换掉了，结果，用 hexo d 的时候，出现错误。

<!--more-->

其错误的结果如下

	On branch master
	nothing to commit, working directory clean
	Fatal: HttpRequestException encountered.
	bash: /dev/tty: No such device or address
	error: failed to execute prompt script (exit code 1)
	fatal: could not read Username for 'https://github.com': Invalid argument
	FATAL Something's wrong. Maybe you can find the solution here: http://hexo.io/docs/troubleshooting.html
	Error: Fatal: HttpRequestException encountered.
	bash: /dev/tty: No such device or address
	error: failed to execute prompt script (exit code 1)
	fatal: could not read Username for 'https://github.com': Invalid argument

		at ChildProcess.<anonymous> (E:\site\blog\node_modules\hexo-util\lib\spawn.js:37:17)
		at ChildProcess.emit (events.js:182:13)
		at ChildProcess.cp.emit (E:\site\blog\node_modules\cross-spawn\lib\enoent.js:40:29)
		at maybeClose (internal/child_process.js:962:16)
		at Process.ChildProcess._handle.onexit (internal/child_process.js:251:5)

其主要的错误是

	Fatal: HttpRequestException encountered.

出现这个错误我猜测可能是 git 的版本太新了，或者其它原因，在这里我直接说解决方法，经过网上查询，原因是

>Github 禁用了TLS v1.0 and v1.1，必须更新Windows的git凭证管理器，才行。 

[下载地址](https://github.com/Microsoft/Git-Credential-Manager-for-Windows/releases/tag/v1.14.0)

{% img /images/hexo/11_0.png %}

经过安装上面的证书，我又可以上传了。