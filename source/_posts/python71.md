---
title: python 自动生成 requirements.txt
date: 2020-02-05 10:45:38
categories:
- python
tags:
- python
- requirements
---
这个还是蛮喜欢的，自动生成。

<!-- more -->

[参考资料](https://www.jb51.net/article/170248.htm)

python项目如何在另一个环境上重新构建项目所需要的运行环境依赖包？

使用的时候边记载是个很麻烦的事情，总会出现遗漏的包的问题，这个时候手动安装也很麻烦，不能确定代码报错的需要安装的包是什么版本。这些问题，requirements.txt都可以解决！

生成requirements.txt，有两种方式：

# 适用于 单虚拟环境的情况

	pip freeze > requirements.txt

为什么只适用于单虚拟环境？因为这种方式，会将环境中的依赖包全都加入，如果使用的全局环境，则下载的所有包都会在里面，不管是不时当前项目依赖的，如下图

{% img /images/python/71_0.png %}

当然这种情况并不是我们想要的，当我们使用的是全局环境时，可以使用第二种方法。

# (推荐) 使用 pipreqs ，

github地址为： https://github.com/bndr/pipreqs


## 安装

	pip install pipreqs

## 在当前目录生成

	pipreqs . --encoding=utf8 --force

注意 

--encoding=utf8 为使用utf8编码，不然可能会报UnicodeDecodeError: 'gbk' codec can't decode byte 0xae in position 406: illegal multibyte sequence 的错误。

--force 强制执行，当 生成目录下的requirements.txt存在时覆盖。

当当当，可以看见我依赖的只有这些啦

{% img /images/python/71_1.png %}