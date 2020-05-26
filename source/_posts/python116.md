---
title: python | macbook 安装 pypy3
date: 2020-05-26 18:24:48
categories:
- python
- 进阶
- pypy
tags:
- pypy
---
在 MacBook 中如何安装 pypy，并且如何使用它。

<!-- more -->

经过资料的一些比对，我决定使用 brew 安装。

## brew 安装

首先，我们进入 brew 的官网，查看是否有相应的 package。

- [package](https://formulae.brew.sh/formula/)

搜索 `pypy3`，可以得到下面的链接。

- [pypy3](https://formulae.brew.sh/formula/pypy3#default)

使用

    brew install pypy3

安装成功后，有这样一段话

    Setuptools and pip have been installed, so you can use easy_install_pypy3 and
    pip_pypy3.
    To update pip and setuptools between pypy3 releases, run:
         pip_pypy3 install --upgrade pip setuptools

之前使用 CPython 的安装扩展模块是

    pip install package

那么，通过上面的安装方式，pypy3 的安装模式是

    pip_pypy3 install package

## pycharm 结合

现在我们已经成功安装了 pypy3，那么，我们如果使用的话，就需要把编译器指向它。

打开，`pycharm` 找到 `Project Interpreter` 然后，选择 `add` 增加一个新的编译器。

我们可以看到左边有很多选项，我们选择 `Virtualenv Environment` 然后选择下面的 `Existing environment`

然后指向 `pypy3` 就好了。

下一步，我们就需要查一下 `pypy3` 安装到哪里了。

    brew list pypy3

出现

    /usr/local/Cellar/pypy3/7.3.0/bin/easy_install_pypy3
    /usr/local/Cellar/pypy3/7.3.0/bin/pip_pypy3
    /usr/local/Cellar/pypy3/7.3.0/bin/pypy3
    /usr/local/Cellar/pypy3/7.3.0/lib/libpypy3-c.dylib
    /usr/local/Cellar/pypy3/7.3.0/libexec/bin/pypy3
    /usr/local/Cellar/pypy3/7.3.0/libexec/include/ (136 files)
    /usr/local/Cellar/pypy3/7.3.0/libexec/lib/libpypy3-c.dylib
    /usr/local/Cellar/pypy3/7.3.0/libexec/lib-python/ (4209 files)
    /usr/local/Cellar/pypy3/7.3.0/libexec/lib_pypy/ (871 files)
    /usr/local/Cellar/pypy3/7.3.0/libexec/site-packages/ (708 files)

可以看到有这么一句

    /usr/local/Cellar/pypy3/7.3.0/bin/pypy3

我们指向它就好了。

经过，我本地测试，是可以的。