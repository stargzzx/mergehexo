---
title: 利用阿里云搭建微信公众号
date: 2019-03-18 17:07:41
categories:
- server
tags:
- server
- 阿里云
- 服务器
- 微信公众号
- ubuntu
---
这是一篇基础服务器搭建，主要用于开发微信公众号。

<!-- more -->

## 参考资料

[官方文档bug，微信公众号号开发基本设置 token验证失败原因~！！！填坑~！！！填坑~！！](https://blog.csdn.net/qq_39866513/article/details/83218731)
[为Ubuntu添加PATH环境变量](http://www.cnblogs.com/pang123hui/archive/2011/05/28/2309889.html)
[使用用户名密码验证连接Linux实例](https://help.aliyun.com/document_detail/25434.html)
[linux 安装pip 和python3](https://www.cnblogs.com/lilidun/p/6041198.html)
[在Ubuntu 14中安装python3.5](https://www.jianshu.com/p/4bed6a1cb3e5)
[web.py](http://webpy.org/)
[更换云服务器上的Python版本](https://yq.aliyun.com/articles/401353)

## 流程

### 购买云服务器

因为阿里云有学生主机，所以，我们就购买了一个，一个月 9.5 元，对于我还是很有压力的，幸好我们一共有三个人这样平摊下来，成本还算是可以接受的。

我们购买的是轻量级服务器。

其配置如图：

![](/images/server/0_0.png)

### 连接服务器

我用的是 putty 和 filezilla

关于这两个的教程，你们可以自行谷歌。

反正我用的是非常爽。

### 原服务器配置

我们的系统是 ubuntu16.04 ，里面的 python 版本是 2.7 和 3.5 ，但是，我们要配置 python 3.6 的，所以我们需要把原来的 python 删掉，然后重新下载。

#### 更新系统，安装所需工具

	sudo apt-get upgrade && apt-get update
	sudo apt-get install gcc make
	
#### 卸载自带的Python

	sudo apt-get remove python2*
		# 卸载 python2
	sudo apt-get remove python3*
		# 卸载 python3
		
#### 下载相应版本的Python源码

[下载地址](https://www.python.org/downloads/source/?spm=a2c4e.11153940.blogcont401353.16.ca287164kJvB5H)

我下载的是 3.6.8 版本的，找到相应的版本下载到服务器上

	wget https://www.python.org/ftp/python/$version/Python-$version.tgz
	
解压并安装

	tar -zxvf Python-$version.tgz && cd Python-$version
	./configure --prefix=path
	make 
	make install 
	
上文的path为你要安装的位置。如果不加，对将来卸载或安装其他版本的Python有影响。

我的 prefix 对应的是 /usr/python ,加入之后，我们还要将 python 的路径添加进去。

	打开~/.bashrc文件最末添加命令：
	PATH=$PATH:/usr/python/bin
	注意与上文中各式不同，没有双引号，重启OK。

这个时候，我们就可以用 python3 来进入 python 的编辑环境。

但是，这个时候我们安装的 python 仅仅只是一个 python ，里面甚至都没有 pip。

所以我们要继续配置。

### 安装 setuptools

我们以后使用 pip 都是建设在 setuptools 上的，所以，我们要先安装这个。

执行

	wget --no-check-certificate  https://pypi.python.org/packages/source/s/setuptools/setuptools-19.6.tar.gz#md5=c607dd118eae682c44ed146367a17e26
	tar -zxvf setuptools-19.6.tar.gz
	cd setuptools-19.6
	python3 setup.py build
	python3 setup.py install
	
但这个时候会报错

	RuntimeError: Compression requires the (missing) zlib module
	
即我们要安装，zlib-devel包，但是在 ubuntu 中 zlib叫zlib1g，相应的zlib-devel叫zlib1g.dev

所以，我们要执行

	apt-get install zlib1g.dev
	
需要对 相应的版本 python3.6 进行重新编译安装。

	cd python3.6
	make & make install

又是漫长的编译安装过程。

重新安装setuptools

	python3 setup.py build
	python3 setup.py install

### 安装 pip

	wget --no-check-certificate  https://pypi.python.org/packages/source/p/pip/pip-8.0.2.tar.gz#md5=3a73c4188f8dbad6a1e6f6d44d117eeb
	tar -zxvf pip-8.0.2.tar.gz
	cd pip-8.0.2
	python3 setup.py build
	python3 setup.py install

如果没有意外的话，pip安装完成。

现在我安装了 pip ，如果要使用的话，应该用 pip3 install ***

## 公众号配置步骤

[入门指引](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1472017492_58YV5)

有一个步骤是安装 web.py

因为我是 python3 所以，命令，应该是


	pip3 install web.py==0.40-dev1
	
上面的命令可以在 web.py doc 中查到。

里面的步骤十分详尽，但是所有的代码都是 python2.7 ，但是由于我用的是 python3.6 ，所以有些代码不太适合，尤其是验证 token 的那一段。

在文档中，其 demo 的代码是：

handle.py

```python
# -*- coding: utf-8 -*-
# filename: handle.py

import hashlib
import web

class Handle(object):
    def GET(self):
        try:
            data = web.input()
            if len(data) == 0:
                return "hello, this is handle view"
            signature = data.signature
            timestamp = data.timestamp
            nonce = data.nonce
            echostr = data.echostr
            token = "xxxx" #请按照公众平台官网\基本配置中信息填写

            list = [token, timestamp, nonce]
            list.sort()
            sha1 = hashlib.sha1()
            map(sha1.update, list)
            hashcode = sha1.hexdigest()
            print "handle/GET func: hashcode, signature: ", hashcode, signature
            if hashcode == signature:
                return echostr
            else:
                return ""
        except Exception, Argument:
            return Argument
```

但是在 python3.6.5 版本中，正确的代码应该是

```python
# -*- coding: utf-8 -*-
# filename: handle.py

import hashlib
import web

class Handle(object):
	def GET(self):
		try:
			data = web.input()
			if len(data) == 0:
				return "hello, this is handle view"
			print(data)
			signature = data.signature
			timestamp = data.timestamp
			nonce = data.nonce
			echostr = data.echostr
			token="yansan2019"

			list = [token, timestamp, nonce]
			list.sort()
			sha1 = hashlib.sha1()
			sha1.update(list[0].encode("utf-8"))
			sha1.update(list[1].encode("utf-8"))
			sha1.update(list[2].encode("utf-8"))
			hashcode = sha1.hexdigest()
			print ("handle/GET func: hashcode, signature: ", hashcode, signature)
			if hashcode == signature:
				return echostr
			else:
				print(222)
				return ""
		except Exception as Argument:
			return Argument
```

## 一些其他的事

### 80端口占用问题

[Linux 查看端口占用情况](http://www.runoob.com/w3cnote/linux-check-port-usage.html)

由于，有时候 putty ，会自动断开，导致程序一直占用 80 端口，我们再次启用这个端口的时候，必须先把前面的程序杀死。

执行下面的程序查找占用端口的 PID

	netstat -tunlp
	
然后我们用命令杀死这个进程。

	kill -9 PID号







