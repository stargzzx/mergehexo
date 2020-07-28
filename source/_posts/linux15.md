---
title: linux | 查看系统状态
date: 2020-07-27 10:08:59
categories:
- [linux,基础]
tags:
- linux
---
这里将罗列一些查看系统状态的命令。

<!-- more -->

## free

free指令会显示内存的使用情况，包括实体内存，虚拟的交换文件内存，共享内存区段，以及系统核心使用的缓冲区等。

	free [-bkmotV][-s <间隔秒数>]

参数说明：

- `-b` 　以Byte为单位显示内存使用情况。
- `-k` 　以KB为单位显示内存使用情况。
- `-m` 　以MB为单位显示内存使用情况。
- `-h` 　以合适的单位显示内存使用情况，最大为三位数，自动计算对应的单位值。单位有：
	- `B`= bytes
	- `K`= kilos
	- `M`= megas
	- `G`= gigas
	- `T`= teras
- `-o` 　不显示缓冲区调节列。
- `-s`<间隔秒数> 　持续观察内存使用状况。
- `-t` 　显示内存总和列。
- `-V` 　显示版本信息。

<br/>

	free -h -s 3

## uptime

使用uptime 命令，获取服务器运行了多久。

	uptime

输出

	14:48:52 up 2 days, 22:15,  1 user,  load average: 0.67, 0.55, 0.55

- 系统时间 14:48:52
- 运行时长 up 2 days, 22:15 「2 天，22小时，15分钟」
- 在线用户 1 user
- 系统负载 load average: 0.67, 0.55, 0.55
	- 对应 1分钟、5分钟、15分钟的负载量
	- 1 分钟是 67%
	- 5 分钟是 55%
	- 15 分钟是 55%
	- 负载越小越好
	
## top

用于实时显示 process 的动态。

使用权限：所有使用者。

具体的指令在这里就不说了，这里只讨论使用

	top

输出

	top - 09:12:38 up 277 days, 22:02,  2 users,  load average: 0.00, 0.00, 0.00
	Tasks: 112 total,   1 running, 111 sleeping,   0 stopped,   0 zombie
	%Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
	KiB Mem :  4046220 total,   192524 free,   133784 used,  3719912 buff/cache
	KiB Swap:        0 total,        0 free,        0 used.  3591528 avail Mem 

	  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND                                                                                                                                
	30851 root      10 -10  133112  17768  14784 S   1.7  0.4 439:09.20 AliYunDun                                                                                                                              
	  212 root      20   0   27220   2716   2388 S   0.3  0.1  24:41.73 systemd-journal                                                                                                                        
	 5773 root      20   0  366292  21184   4984 S   0.3  0.5   1129:52 containerd                                                                                                                             
	30826 root      10 -10   32388   6372   5620 S   0.3  0.2  13:59.54 AliYunDunUpdate                                                                                                                        
	    1 root      20   0   38272   5964   3660 S   0.0  0.1   3:55.42 systemd                                                                                                                                
	    2 root      20   0       0      0      0 S   0.0  0.0   0:02.06 kthreadd                                                                                                                               
	    3 root      20   0       0      0      0 S   0.0  0.0   1:57.92 ksoftirqd/0                                                                                                                            
	    5 root       0 -20       0      0      0 S   0.0  0.0   0:00.00 kworker/0:0H                                                                                                                           
	    7 root      20   0       0      0      0 S   0.0  0.0 138:24.02 rcu_sched  

拆解含义

### 第一行

	top - 09:12:38 up 277 days, 22:02,  2 users,  load average: 0.00, 0.00, 0.00

|内容|	含义|
|---|---|
|09:12:38|	表示当前时间|
|up 277 days|	系统运行时间|
|2 users|	当前登录用户数|
|load average: 0.00, 0.00, 0.00|	系统负载，即任务队列的平均长度。 三个数值分别为 1分钟、5分钟、15分钟前到现在的平均值。|

### 第二三行

	Tasks: 112 total,   1 running, 111 sleeping,   0 stopped,   0 zombie
	%Cpu(s):  0.3 us,  0.3 sy,  0.0 ni, 99.3 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st

第2、3行为进程和CPU的信息 

当有多个CPU时，这些内容可能会超过两行，其参数如下

|内容|	含义|
|---|---|
|112 total|	进程总数|
|1 running|	正在运行的进程数|
|111 sleeping|	睡眠的进程数|
|0 stopped|	停止的进程数|
|0 zombie|	僵尸进程数|
|0.3 us|	用户空间占用CPU百分比|
|0.3 sy|	内核空间占用CPU百分比|
|0.0 ni|	用户进程空间内改变过优先级的进程占用CPU百分比|
|99.3 id|	空闲CPU百分比|
|.0 wa	|等待输入输出的CPU时间百分比|
|.0 hi	|硬中断（Hardware IRQ）占用CPU的百分比|
|.0 si	|软中断（Software Interrupts）占用CPU的百分比|
|.0 st	 ||

### 第四五行

	KiB Mem :  4046220 total,   192524 free,   133784 used,  3719912 buff/cache
	KiB Swap:        0 total,        0 free,        0 used.  3591528 avail Mem 

|内容|含义|
|---|---|
|KiB Mem: 4046220 total|	物理内存总量|
|192524 free|	空闲内存总量|
|133784 used|	使用的物理内存总量|
|3719912 buffers（buff/cache）|	用作内核缓存的内存量|
|KiB Swap:   0 total|	交换区总量|
|0 used	|使用的交换区总量|
|0 free	|空闲交换区总量|
|0 cached Mem|	缓冲的交换区总量。|
|0 avail Mem|	代表可用于进程下一次分配的物理内存数量|

### 进程信息

|列名|	含义|
|---|---|
|PID|	进程id|
|PPID|	父进程id|
|RUSER|	Real user name|
|UID	|进程所有者的用户id|
|USER|	进程所有者的用户名|
|GROUP|	进程所有者的组名|
|TTY|	启动进程的终端名。不是从终端启动的进程则显示为 ?|
|PR|	优先级|
|NI|	nice值。负值表示高优先级，正值表示低优先级|
|P|	最后使用的CPU，仅在多CPU环境下有意义|
|%CPU|	上次更新到现在的CPU时间占用百分比|
|TIME|	进程使用的CPU时间总计，单位秒|
|TIME+|	进程使用的CPU时间总计，单位1/100秒|
|%MEM|	进程使用的物理内存百分比|
|VIRT|	进程使用的虚拟内存总量，单位kb。VIRT=SWAP+RES|
|SWAP|	进程使用的虚拟内存中，被换出的大小，单位kb|
|RES|	进程使用的、未被换出的物理内存大小，单位kb。RES=CODE+DATA|
|CODE|	可执行代码占用的物理内存大小，单位kb|
|DATA|	可执行代码以外的部分(数据段+栈)占用的物理内存大小，单位kb|
|SHR|	共享内存大小，单位kb|
|nFLT|	页面错误次数|
|nDRT|	最后一次写入到现在，被修改过的页面数。|
|S|	进程状态。D=不可中断的睡眠状态 R=运行 S=睡眠 T=跟踪/停止 Z=僵尸进程|
|COMMAND|	命令名/命令行|
|WCHAN|	若该进程在睡眠，则显示睡眠中的系统函数名|
|Flags|	任务标志|

在 top 的视图下，按 `1` 可以出现每一个 `cpu` 的情况

	top - 09:26:35 up 277 days, 22:16,  3 users,  load average: 0.17, 0.09, 0.02
	Tasks: 115 total,   1 running, 114 sleeping,   0 stopped,   0 zombie
	%Cpu0  :  0.3 us,  0.3 sy,  0.0 ni, 99.0 id,  0.0 wa,  0.0 hi,  0.3 si,  0.0 st
	%Cpu1  :  0.3 us,  0.7 sy,  0.0 ni, 99.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
	KiB Mem :  4046220 total,   189248 free,   136904 used,  3720068 buff/cache
	KiB Swap:        0 total,        0 free,        0 used.  3588332 avail Mem 
