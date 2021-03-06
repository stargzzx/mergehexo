---
title: 树莓派命令行的使用
date: 2018-12-26 09:28:00
categories:
- raspberry
tags:
- 树莓派
- raspberry
- linux
---
如题，这篇文章我会持续更新。

<!--more-->

## 切换命令行和图形化界面

	ctrl + alt + F1 ~ F5 都是切换到命令行界面
	ctrl + alt + f6 是切换到图形化界面
	
## 对命令行布局的解释

![](/images/raspberry/2_0.png)

## 目录操作

### cd

切换到用户目录下（在树莓派中，我们默认的用户是 pi）,下面两条命令都是转到 pi 目录下

	cd /home/pi/
	cd ~
	
如果传给 cd 命令开头是以下划线开头，则是绝对路径。相对路径通常为

	. 表示当前目录
	.. 表示上一层目录
	
切换到根目录

	cd /
	
### cp

拷贝文件

	cp a b
	拷贝 a 文件 到 b 文件
	
### ls

ls 命令可以列出当前目录下的文件

它有很多参数，比如

	-l 可以让显示的文件列表更加详尽
	-a 可以列出所有文件，包含隐藏文件
	他们可以组合使用
	ls -la
	
### touch

可以用于创建一个新的空文件

	touch foo.txt
	
### mkdir

创建新目录

	mkdir dir
	
### tar

将一个目录下的所有文件进行打包，但是通常除了 tar 一此外，还得在通过 gzip 进行压缩一次。

整个操作如下：

	tar -cf mydir.tar mydir
	gzip mydir.tar
	
这样就把 mydir 目录打包并压缩成一个名为 mydir.tar.gz 的文件。

### rm

删除文件

	rm foo.txt
	
这个命令也可以删除非空目录，加上 -r 的参数后，就可以递归的删除目录中的文件

	rm -r dir
	
### rmdir

删除空目录，这里划重点，是删除空目录

### mv

改变文件的名字还有移动文件

	mv foo.txt aaa.py 这个是修改文件名
	mv foo.txt dir 如果有 dir 这个目录，那么foo.txt 会移动到这个目录下
	mv foo.txt dir/test.txt 移动到 dir 目录下，并且重新命名
	

## 查询

### man

可以了解一个命令所包含的参数，也可以在命令后面添加参数 --help

下面这两个命令是等价的

	man ls
	ls --help
	
但是，ls --help 显示的是从开头瞬间到了结尾，所以你只能看最后几行显示情况，所以可以用下面的命令行替换，这样就和 man ls 一样了。

	ls --help | less
	
## 输入输出 IO
	
### 管道 |

管道是把两个程序联系在一起，通过管道，可以把其中一个程序的输出作为另一个程序的输入。

所有的 linux 程序都可以从标准输入（通常表示为 stdin） 读入数据，像标准输出（表示为 stdout）输出数据并把错误信息抛向标准错误输出（表示为 stderr）。

通过管道可以把一个程序的 stdout 和另一个程序的 stdin 相连。

管道的操作符号是 | ，在管道中如果要退出可以按 Q 键。

这里需要注意的是，主体是程序，你不能把一个文件当作程序，如下面的的这行命令是错的

	ls -la | foo.txt
	
但是，通常 linux 的一个命令输出如果很多的话，会直接从开头输出到结束，而不会给人段落阅读的流程，所以，我们会将输出到达 less，这样我们就可以进行段落阅读。

	ls -la | less
	less 是 linux 的一个程序
	
![](/images/raspberry/2_1.png)

### 重定向

重定向就是把程序的标准输出发送到一个文件中去，用 > 来表示

	ls -la > foo.txt
	这行命令使得输出不在命令行中，而是输入到了 foo.txt 中。
	
### cat

如果想把一件文件的内容一次性全部输出到标准输出上，可以用 cat 命令。相当于 print

在需要的时候把一个文件的内容传到另一个程序或者把它重定向到其它地方，这个命令很有用。

	cat foo.txt > aaa.txt
	将两个文件内容传到一个文件中
	cat foo.txt aaa.txt > bbb.txt

### tail

查看一个文件的最后10行

我们也可以修改行数

	tail -20
	
### head

查看一个文件的前10行

修改行数

	head -10
	

### grep

在一个文件或者多个文件中搜索一个特定的字符

	grep Puzzle */*
	
## 系统相关

### 查看系统版本
	
	cat /proc/version
	
### 查看主板版本

	cat /proc/cpuinfo
	
### 开机关机相关

	sudo reboot
	sudo shutdown -h now
	
### 配置环境

	sudo raspi-config
	
### top

我们启动树莓派的时候，系统会运行大概 75 个进程，每一个进程负责一项任务或提供一个服务。

可以用 top 命令查看正在运行的进程，与此同时也可以看到 CPU 和内存中使用情况。

top 命令能让你看到占用资源最多的进程。

### ps

ps 命令可以用来列出所有进程和它们对应的进程 ID（PID）

	ps -aux | less
	
### kill

如果要中断一个出错或失去响应的进程，可以用 ps 命令找出这个进程的 ID ,然后用 kill 命令来中断它的运行

	kill 5687
	
### sudo

Linux 是一个多用户的操作系统，每一个用户都可以创建自己的文件，目录，或者修改。

但是 root（管理员）用户可以修改文件系统中任何一个文件。

如果以 pi 用户登录，很难对系统造成什么致命性的破坏。

通过使用 sudo 或者类似的工具可以使普通用户临时获得管理员权限，用来执行安装软件包之类的操作，这样就可以规避直接以 root 用户登录所带来的安全风险。

系统中每一个文件都属于某一个用户或者一个用户组，我们可以用 chown 和 chgrp 命令可以修改文件所属的用户和用户组。要执行这两个命令，我们必须要有 root 权限。

	sudo chown pi foo.txt
	sudo chgrp staff plugh.txt
	
关于文件权限和其他请看下面的图片：

![](/images/raspberry/2_2.png)

### chmod

可以用这个命令设置文件的权限。

![](/images/raspberry/2_3.png)

相关命令如下

	chmod u+rwx,g-rwx,o-rwx foo.txt
	
## 网络

### ifconfig

这个命令可以显示你所有的网络接口以及他们上面所绑定的 IP 地址。

![](/images/raspberry/2_4.png)

## 编辑

我们用 nano 编辑器来编辑文件。

nano是一个字符终端的文本编辑器，有点像DOS下的editor程序。它比vi/vim要简单得多，比较适合Linux初学者使用。某些Linux发行版的默认编辑器就是nano。

nano命令可以打开指定文件进行编辑，默认情况下它会自动断行，即在一行中输入过长的内容时自动拆分成几行，但用这种方式来处理某些文件可能会带来问题，比如Linux系统的配置文件，自动断行就会使本来只能写在一行上的内容折断成多行了，有可能造成系统不灵了。因此，如果你想避免这种情况出现，就加上-w选项吧。

nano [选项] [[+行,列] 文件名]...

![](/images/raspberry/2_5.png)

	nano foo.txt

使用技巧

	复制一整行：Alt+6
	剪贴一整行：Ctrl+K
	粘贴：Ctrl+U
		如果需要复制／剪贴多行或者一行中的一部分，
		先将光标移动到需要复制／剪贴的文本的开头，按Ctrl+6（或者Alt+A）做标记，
		然后移动光标到 待复制／剪贴的文本末尾。这时选定的文本会反白，用Alt+6来复制，Ctrl+K来剪贴。
		若在选择文本过程中要取消，只需要再按一次Ctrl+6。
	搜索
		按Ctrl+W，然后输入你要搜索的关键字，回车确定。这将会定位到第一个匹配的文本，接着可以用Alt+W来定位到下一个匹配的文本。
	Ctrl+Y到上一页
	Ctrl+V到下一页
	保存 使用Ctrl+O来保存所做的修改
	退出 按Ctrl+X
		如果你修改了文件，下面会询问你是否需要保存修改。
		输入Y确认保存，输入N不保存，按Ctrl+C取消返回。
		如果输入了Y，下一步会让你输入想要保存的文件名。
		如果不需要修改文件名直接回车就行；若想要保存成别的名字（也就是另存为）则输入新名称然后确 定。
		这个时候也可用Ctrl+C来取消返回。
		
## 安装软件

Raspbian 默认只安装了很少的一些软件，所以我们需要自主的安装一些软件。

apt-get 程序带上 install 参数是用于下载软件包的。

安装软件包需要管理员权限，所以，需要带上 sudo 参数。

	sudo apt-get install emacs
		安装 emcas 编辑器
		
## 小技巧

	tab 自动补全
	ctrl + c 中断当前正在运行的程序，在有些交互性应用程序，比如文本编辑器中可能无效
	ctrl + d 退出 shell ，必须在命令提示符后紧跟着输入才有效
	ctrl + a 将光标移到行首
	ctrl + e 将光标移到行尾
	















