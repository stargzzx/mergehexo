---
title: Anaconda 所遇到的错误
date: 2019-01-20 16:03:03
categories:
- python
tags:
- python
- 第三方版本
- Anaconda
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















