---
title: linux | 环境变量相关「永久和暂时」
date: 2020-07-08 14:06:45
categories:
- [linux,基础]
tags:
- linux
---
这个是基础。

<!-- more -->

<br/>

# Linux环境变量分类

<br/>

## 按照生命周期来分

Linux环境变量可以分为两类：

- `永久的` 需要用户修改相关的配置文件，变量永久生效。
- `临时的` 用户利用export命令，在当前终端下声明环境变量，关闭Shell终端失效。

## 按照作用域来分

Linux环境变量可以分为：

- `系统环境变量` 系统环境变量对该系统中所有用户都有效。
- `用户环境变量` 顾名思义，这种类型的环境变量只对特定的用户有效。

<br/>

# 永久有效

<br/>

## 方法一

在 `/etc/profile` 文件中添加变量【对`所有用户`生效（永久的）】  

用vim在文件`/etc/profile`文件中增加变量，该变量将会对Linux下所有用户有效，并且是“永久的”。  

例如：编辑/etc/profile文件，添加CLASSPATH变量

	vim /etc/profile    
	export CLASSPATH=./JAVA_HOME/lib;$JAVA_HOME/jre/lib

要让刚才的修改马上生效，需要执行以下代码  

	source /etc/profile  

## 方法二

在用户目录下的`.bash_profile`文件中增加变量【对单一用户生效（永久的）】  

用vim在用户目录下的.bash_profile文件中增加变量，改变量仅会对当前用户有效，并且是“永久的”。  

	vim ~/.bash.profile
	export CLASSPATH=./JAVA_HOME/lib;$JAVA_HOME/jre/lib

要让刚才的修改马上生效，需要在用户目录下执行以下代码  

	source .bash_profile

<br/>

# 临时有效

<br/>

直接运行`export`命令定义变量 【只对当前shell（BASH）有效（临时的）】

在shell的命令行下直接使用 `export 变量名=变量值`

定义变量，该变量只在当前的shell（BASH）或其子shell（BASH）下是有效的，shell关闭了，变量也就失效了，再打开新shell时就没有这个变量，需要使用的话还需要重新定义。

<br/>

# Linux环境变量使用

<br/>

## Linux中常见的环境变量有

- `PATH`：指定命令的搜索路径

PATH声明用法

	PATH=$PAHT:<PATH 1>:<PATH 2>:<PATH 3>:--------:< PATH n >
	export PATH
	你可以自己加上指定的路径，中间用冒号隔开。环境变量更改后，在用户下次登陆时生效。
	可以利用echo $PATH查看当前当前系统PATH路径。

- `HOME`：指定用户的主工作目录（即用户登陆到Linux系统中时，默认的目录）。
- `HISTSIZE`：指保存历史命令记录的条数。
- `LOGNAME`：指当前用户的登录名。
- `HOSTNAME`：指主机的名称，许多应用程序如果要用到主机名的话，通常是从这个环境变量中来取得的
- `SHELL`：指当前用户用的是哪种Shell。
- `LANG/LANGUGE`：和语言相关的环境变量，使用多种语言的用户可以修改此环境变量。
- `MAIL`：指当前用户的邮件存放目录。

注意：上述变量的名字并不固定，如HOSTNAME在某些Linux系统中可能设置成HOST

## Linux也提供了修改和查看环境变量的命令

下面通过几个实例来说明：

- `echo` 显示某个环境变量值 echo $PATH
- `export` 设置一个新的环境变量 export HELLO="hello" (可以无引号)
- `env` 显示所有环境变量
- `set` 显示本地定义的shell变量
- `unset` 清除环境变量 unset HELLO
- `readonly` 设置只读环境变量 readonly HELLO

如果你配置了 `zsh` 请看一下我下面的博文。

- [MacBook下的z-shell](https://benpaodewoniu.github.io/2019/10/03/macbook4/)

<br/>

# \.bashrc文件和\.bash\_profile文件的区别

<br/>

- `~/.bash_profile` 是交互式、`login` 方式进入 `bash` 运行的
- `~/.bashrc` 是交互式 `non-login` 方式进入 `bash`

运行的通常二者设置大致相同，所以通常前者会调用后者。所以一般优先把变量设置在`.bashrc`里面。比如在`crontab`里面执行一个命令，`.bashrc`设置的环境变量会生效，而`.bash_profile`不会。

简单的说，`.bash_profile`只在会话开始时被读取一次，而`.bashrc`则每次打开新的终端时，都会被读取。

