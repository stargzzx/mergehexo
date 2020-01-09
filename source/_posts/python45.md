---
title: Anaconda 所遇到的错误
date: 2019-01-20 16:03:03
categories:
- python
tags:
- python
- 第三方版本
- anaconda
---
如题，给后来者填坑。

<!-- more -->

## 参考资料

[Python管理包工具anaconda安装过程常见问题解决办法](https://zhuanlan.zhihu.com/p/34337889)

## 更新包命令报错

如果运行以下更新包命令：

	conda upgrade    --all
	
报下面的错误类型：connecttimeout

{% img /images/python/45_0.jpg %}

【方法一】可以用下面的解决办法换成清华源来下载更新（在终端中运行以下命令）：

[镜像源地址](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
	conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
	conda config --set show_channel_urls yes
	
如果上面命令运行后没有报错，说明替换清华源成功。再运行更新包命令就可以了：

	conda upgrade    --all
	
【方法二】如果上面命令行方法运行后报错，通过配置文件来替换成清华源

打开“notebook工作文件夹”找到文件.condarc。

什么是你的“notebook工作文件夹”呢？

默认情况下，是你启动Anaconda Prompt终端中的那个文件夹，比如我电脑上是下面这个文件夹（Windows为C://Users/username/.condarc，Linux/Mac为~/.condarc）：

{% img /images/python/45_1.jpg %}

打开该文件夹，修改文件内容如下：

（如果没有找到.condarc，用notepad++软件在“notebook工作文件夹”下新建一个该文件即可，并添加以下内容）

{% img /images/python/45_2.jpg %}

为了方便，你可以将下面部分直接复制到上面的文件中：

	channels:
	- https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
	- https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
	- https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
	show_channel_urls: true

## 报下面的错误类型：proxyerror

{% img /images/python/45_3.jpg %}

把vpn代理关了，然后重新运行就可以了。

## conda安装不是包

有时候用conda安装不上包，我们可以使用pip来安装。比如我在安装股票数据pandas_datareader包时，就使用pip来安装才成功。

{% img /images/python/45_4.jpg %}

## Connection refused

我是公司的集群上，安装 anaconda3 之后，当正确的安装后，我创建环境，报了这个错误：

{% codeblock %}
 ./conda create -n py3.6 python=3.6
Collecting package metadata (current_repodata.json): failed

CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://repo.anaconda.com/pkgs/main/linux-64/current_repodata.json>
Elapsed: -

An HTTP error occurred when trying to retrieve this URL.
HTTP errors are often intermittent, and a simple retry will get you on your way.

If your current network has https://www.anaconda.com blocked, please file
a support request with your network engineering team.

ConnectionError(MaxRetryError("SOCKSHTTPSConnectionPool(host='repo.anaconda.com', port=443): Max retries exceeded with url: /pkgs/main/linux-64/current_repodata.json (Caused by NewConnectionError('<urllib3.contrib.socks.SOCKSHTTPSConnection object at 0x7fcfc46e0910>: Failed to establish a new connection: [Errno 111] Connection refused'))"))
{% endcodeblock %}

下面是解决方案：

经过我很长时间的摸索，我发现上面的 url

	https://repo.anaconda.com/pkgs/main/linux-64/current_repodata.json

是 404

正确的 url 是

	https://repo.anaconda.com/pkgs/main/linux-64/repodata.json

但是，我看了一眼我 macbook 的配置，其访问的 URL 确实是 current_repodata.json ，但是，我没有其他办法，只能先修改这个文件。

我遍寻了全网的资料，尽然没有一个关于如何修改这个的介绍，当时，都快逼疯了。

终于我看到一条命令

	conda config --show

这个 config 上面的所有属性都可以在 .condarc 中配置，我终于找到了一条属性：

	repodata_fns:
  	  - current_repodata.json
          - repodata.json

柳暗花明又一村，我在 .condarc 中添加了一条

	repodata_fns:
  	  - repodata.json

然后，使用 conda create 创建环境，结果

出现

{% codeblock %}
./conda create -n py3.6 python=3.6
Collecting package metadata (repodata.json): failed

CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://repo.anaconda.com/pkgs/main/linux-64/repodata.json>
Elapsed: -

An HTTP error occurred when trying to retrieve this URL.
HTTP errors are often intermittent, and a simple retry will get you on your way.

If your current network has https://www.anaconda.com blocked, please file
a support request with your network engineering team.

ConnectionError(MaxRetryError("SOCKSHTTPSConnectionPool(host='repo.anaconda.com', port=443): Max retries exceeded with url: /pkgs/main/linux-64/repodata.json (Caused by NewConnectionError('<urllib3.contrib.socks.SOCKSHTTPSConnection object at 0x7f579443de50>: Failed to establish a new connection: [Errno 111] Connection refused'))"))
{% endcodeblock %}

问题还没解决，或者说，没有解决全部的问题。

经过，我的老大过来解围，发现是 代理问题。

集群的 http 走的是代理通道，而，我又没有集群上代理的权限，导致，我 http 下载的文件都被代理拒绝。

后来，把集群上的代理关了之后，就好了。

关于如何关闭代理，我没看懂。。。老大的手太快了。



