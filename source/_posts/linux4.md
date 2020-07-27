---
title: linux 的磁盘管理
date: 2020-01-01 18:03:20
categories:
- [linux,基础]
tags:
- linux
- 磁盘
- ubuntu
---
友情提示一下，下面的命令我都是在 ubuntu 下运行的。

<!-- more -->

<br/>

# df

<br/>

df 以磁盘分区为单位查看文件系统，可以获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

例如，我们使用df -h命令来查看磁盘信息， -h 选项为根据大小适当显示：

![](/images/linux/4_0.png)

显示内容参数说明：

	Filesystem：文件系统
	Size： 分区大小
	Used： 已使用容量
	Avail： 还可以使用的容量
	Use%： 已用百分比
	Mounted on： 挂载点　

相关命令：

	df -hl：
		查看磁盘剩余空间
	df -h：
		查看每个根路径的分区大小
	du -sh [目录名]：
		返回该目录的大小
	du -sm [文件夹]：
		返回该文件夹总M数
	du -h [目录名]：
		查看指定文件夹下的所有文件大小（包含子文件夹）

<br/>

# du

<br/>

du 的英文原义为 disk usage，含义为显示磁盘空间的使用情况，用于查看当前目录的总大小。

- `-a` 或 `-all`  显示目录中个别文件的大小。  
- `-b` 或 `-bytes`  显示目录或文件大小时，以byte为单位。  
- `-c` 或 `--total`  除了显示个别目录或文件的大小外，同时也显示所有目录或文件的总和。
- `-k` 或 `--kilobytes`  以KB(1024bytes)为单位输出
- `-m` 或 `--megabytes`  以MB为单位输出。  
- `-s` 或 `--summarize`  仅显示总计，只列出最后加总的值
- `-h` 或 `--human-readable`  以K，M，G为单位，提高信息的可读性
- `-x` 或 `--one-file-xystem`  以一开始处理时的文件系统为准，若遇上其它不同的文件系统目录则略过。
- `-L` <符号链接>或--dereference<符号链接> 显示选项中所指定符号链接的源文件大小。  
- `-S` 或 `--separate-dirs`   显示个别目录的大小时，并不含其子目录的大小。
- `-X` <文件>或--exclude-from=<文件>  在<文件>指定目录或文件。  
- `--exclude=<目录或文件>`         略过指定的目录或文件。   
- `-D` 或 `--dereference-args`   显示指定符号链接的源文件大小。  
- `-H` 或 `--si`  与-h参数相同，但是K，M，G是以1000为换算单位。  
- `-l` 或 `--count-links`   重复计算硬件链接的文件。  
- `-–max-depth=<目录层数>` 超过指定层数的目录后，予以忽略。

## du

	du

输出

	608     ./test6
	308     ./test4
	4       ./scf/lib
	4       ./scf/service/deploy/product
	4       ./scf/service/deploy/info
	12      ./scf/service/deploy
	16      ./scf/service
	4       ./scf/doc
	4       ./scf/bin
	32      ./scf
	8       ./test3
	1288    .

只显示当前目录下面的子目录的目录大小和当前目录的总的大小，最下面的 `1288` 为当前目录的总大小

## du -sh

	605M    .
		显示指定文件所占空间：


## du log2012.log 

	300     log2012.log

方便阅读的格式显示test目录所占空间情况：

## du -h test

	608K    test/test6
	308K    test/test4
	4.0K    test/scf/lib
	4.0K    test/scf/service/deploy/product
	4.0K    test/scf/service/deploy/info
	12K     test/scf/service/deploy
	16K     test/scf/service
	4.0K    test/scf/doc
	4.0K    test/scf/bin
	32K     test/scf
	8.0K    test/test3
	1.3M    test

如果要查看某一路径下文件夹的大小，我们就需要指定目录深度，不然的话，就会给我们列出所有的目录大小。

## du -h \-\-max-depth=1 /home

max-depth 这个是目录深度

命令在这里重写一遍，因为，有一个我复制标题竟然错了

    du -h --max-depth=1 /home

![](/images/linux/4_1.png)

<br/>

# df 和 du 的区别

<br/>

`du，disk usage`,是通过搜索文件来计算每个文件的大小然后累加，du能看到的文件只是一些当前存在的，没有被删除的。他计算的大小就是当前他认为存在的所有文件大小的累加和。

`df，disk free`，通过文件系统来快速获取空间大小的信息，当我们删除一个文件的时候，这个文件不是马上就在文件系统当中消失了，而是暂时消失了，当所有程序都不用时，才会根据OS的规则释放掉已经删除的文件， df记录的是通过文件系统获取到的文件的大小，他比du强的地方就是能够看到已经删除的文件，而且计算大小的时候，把这一部分的空间也加上了，更精确了。当文件系统也确定删除了该文件后，这时候du与df就一致了。

<br/>

# fdisk

<br/>

`fdisk` 是一个创建和维护分区表的程序，它兼容DOS类型的分区表、BSD或者SUN类型的磁盘列表。

	fdisk [必要参数][选择参数]

	fdisk -l
	显示当前分区情况