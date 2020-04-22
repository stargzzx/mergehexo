---
title: Redis | 配置和使用
date: 2020-04-22 15:43:04
categories:
- redis
tags:
- redis
---
在上一篇

[Redis | 安装](https://benpaodewoniu.github.io/2020/04/21/redis1/)

介绍了如何开启服务，在这一节中，我们讲述一些细节性的东西，比如配置、临时开启服务、客户端等。

<!-- more -->

<br/>

# MacBook

<br/>

## redis服务器的临时启动、使用和退出

### 临时启动

我们

	cd /usr/local/bin/
	ls | grep redis

就会列出下面的列表

- redis-benchmark
- redis-check-aof
- redis-check-rdb
- redis-cli
- redis-sentinel
- redis-server
- dump.rdb 用于将缓存以文件的形式存储在硬盘中

其中 redis-server 是服务端，redis-cli 是客户端。

#### 配置文件

	cd /usr/local/etc/
	ls

我们发现有一个文件叫做

- redis.conf

这个就是配置文件。

临时启动的代码如下

	/usr/local/bin/redis-server /usr/local/etc/redis.conf

会出现下面的字符：

	81646:C 22 Apr 2020 15:04:34.173 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
	81646:C 22 Apr 2020 15:04:34.173 # Redis version=5.0.7, bits=64, commit=00000000, modified=0, pid=81646, just started
	81646:C 22 Apr 2020 15:04:34.173 # Configuration loaded
	81646:M 22 Apr 2020 15:04:34.175 * Increased maximum number of open files to 10032 (it was originally set to 256).
	                _._                                                  
	           _.-``__ ''-._                                             
	      _.-``    `.  `_.  ''-._           Redis 5.0.7 (00000000/0) 64 bit
	  .-`` .-```.  ```\/    _.,_ ''-._                                   
	 (    '      ,       .-`  | `,    )     Running in standalone mode
	 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
	 |    `-._   `._    /     _.-'    |     PID: 81646
	  `-._    `-._  `-./  _.-'    _.-'                                   
	 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
	 |    `-._`-._        _.-'_.-'    |           http://redis.io        
	  `-._    `-._`-.__.-'_.-'    _.-'                                   
	 |`-._`-._    `-.__.-'    _.-'_.-'|                                  
	 |    `-._`-._        _.-'_.-'    |                                  
	  `-._    `-._`-.__.-'_.-'    _.-'                                   
	      `-._    `-.__.-'    _.-'                                       
	          `-._        _.-'                                           
	              `-.__.-'                                               
	81646:M 22 Apr 2020 15:04:34.175 # Server initialized
	81646:M 22 Apr 2020 15:04:34.176 * DB loaded from disk: 0.000 seconds

### 查看redis服务是否启动

	ps aux | grep redis

出现

	licong           81646   0.0  0.0  4295836   2284 s006  S+    3:04下午   0:00.03 /usr/local/bin/redis-server 127.0.0.1:6379 

### 退出

因为只是临时启动，所以，只需要

	command + c

就好了。

## redis 客户端

redis 客户端就是 cd /usr/local/bin/ 下的

- redis-cli

直接在 zsh 中敲入 

	redis-cli

就进入客户端了。

### 用法

常用命令

- keys * 查看所有键值
- set (key) (value) 设置键key的值为value
- append (key) (value2) 在键key的值后面加上value2
- get (key) 查看键key的值

### 退出

	command + c

## 配置文件

[参考资料](https://www.runoob.com/redis/redis-conf.html)

### 编辑配置

你可以通过修改 redis.conf 文件或使用 CONFIG set 命令来修改配置。

语法

	CONFIG SET 命令基本语法：
	redis 127.0.0.1:6379> CONFIG SET CONFIG_SETTING_NAME NEW_CONFIG_VALUE
	实例
	redis 127.0.0.1:6379> CONFIG SET loglevel "notice"
	OK
	redis 127.0.0.1:6379> CONFIG GET loglevel
	1) "loglevel"
	2) "notice"

### 配置字段

|配置项|	说明|
|---|---|
|daemonize no|Redis 默认不是以守护进程的方式运行，可以通过该配置项修改，<br/>使用 yes 启用守护进程（Windows 不支持守护线程的配置为 no ）|
|pidfile /var/run/redis.pid|	当 Redis 以守护进程方式运行时，<br/>Redis 默认会把 pid 写入 /var/run/redis.pid 文件，可以通过 pidfile 指定|
|port 6379|	指定 Redis 监听端口，默认端口为 6379.<br/>作者在自己的一篇博文中解释了为什么选用 6379 作为默认端口，因为 6379 在手机按键上 MERZ 对应的号码，而 MERZ 取自意大利歌女的名字|
|bind 127.0.0.1|	绑定的主机地址|
|timeout 300|	当客户端闲置多长秒后关闭连接，如果指定为 0 ，表示关闭该功能|
|loglevel notice|指定日志记录级别，Redis 总共支持四个级别：debug、verbose、notice、warning，默认为 notice|
|logfile stdout|日志记录方式，默认为标准输出，<br/>如果配置 Redis 为守护进程方式运行，而这里又配置为日志记录方式为标准输出，则日志将会发送给 /dev/null|
|databases 16|	设置数据库的数量，默认数据库为0，可以使用SELECT 命令在连接上指定数据库id|
|save < seconds> < changes>Redis 默认配置文件中提供了三个条件：<br/>save 900 1<br/>save 300 10<br/>save 60 10000<br/>分别表示 900 秒（15 分钟）内有 1 个更改，300 秒（5 分钟）内有 10 个更改以及 60 秒内有 10000 个更改。<br/>指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合|指定在多长时间内，有多少次更新操作，就将数据同步到数据文件，可以多个条件配合|
|rdbcompression yes|指定存储至本地数据库时是否压缩数据，默认为 yes，<br/>Redis 采用 LZF 压缩，如果为了节省 CPU 时间，可以关闭该选项，但会导致数据库文件变的巨大|
|dbfilename dump.rdb|指定本地数据库文件名，默认值为 dump.rdb|
|dir ./	|指定本地数据库存放目录|
|slaveof < masterip> < masterport>|设置当本机为 slav 服务时，设置 master 服务的 IP 地址及端口，在 Redis 启动时，它会自动从 master 进行数据同步|
|masterauth < master-password>|	当 master 服务设置了密码保护时，slav 服务连接 master 的密码|
|requirepass foobared|设置 Redis 连接密码.<br/>如果配置了连接密码，客户端在连接 Redis 时需要通过 AUTH < password> 命令提供密码，<b style="color: red">默认关闭</b>|
|maxclients 128|	设置同一时间最大客户端连接数，默认无限制.<br/>Redis 可以同时打开的客户端连接数为 Redis 进程可以打开的最大文件描述符数，如果设置 maxclients 0，表示不作限制。<br/>当客户端连接数到达限制时，Redis 会关闭新的连接并向客户端返回 max number of clients reached 错误信息|
|maxmemory < bytes>|	指定 Redis 最大内存限制，Redis 在启动时会把数据加载到内存中，达到最大内存后。<br/>Redis 会先尝试清除已到期或即将到期的 Key，当此方法处理 后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。<br/>Redis 新的 vm 机制，会把 Key 存放内存，Value 会存放在 swap 区|
|appendonly no|	指定是否在每次更新操作后进行日志记录，Redis 在默认情况下是异步的把数据写入磁盘。<br/>如果不开启，可能会在断电时导致一段时间内的数据丢失。<br/>因为 redis 本身同步数据文件是按上面 save 条件来同步的，所以有的数据会在一段时间内只存在于内存中。默认为 no|
|appendfilename appendonly.aof|	指定更新日志文件名，默认为 appendonly.aof|
|appendfsync everyse|指定更新日志条件，共有 3 个可选值：<br/>no：表示等操作系统进行数据缓存同步到磁盘（快）<br/>always：表示每次更新操作后手动调用 fsync() 将数据写到磁盘（慢，安全）<br/>everysec：表示每秒同步一次（折中，默认值）|
|vm-enabled no|	指定是否启用虚拟内存机制，默认值为 no，简单的介绍一下，VM 机制将数据分页存放，由 Redis 将访问量较少的页即冷数据 swap 到磁盘上，访问多的页面由磁盘自动换出到内存中（在后面的文章我会仔细分析 Redis 的 VM 机制）|
|vm-swap-file /tmp/redis.swap|	虚拟内存文件路径，默认值为 /tmp/redis.swap，不可多个 Redis 实例共享|
|vm-max-memory 0|	将所有大于 vm-max-memory 的数据存入虚拟内存，无论 vm-max-memory 设置多小，所有索引数据都是内存存储的(Redis 的索引数据 就是 keys)，也就是说，当 vm-max-memory 设置为 0 的时候，其实是所有 value 都存在于磁盘。默认值为 0|
|vm-page-size 32|	Redis swap 文件分成了很多的 page，一个对象可以保存在多个 page 上面，但一个 page 上不能被多个对象共享，vm-page-size 是要根据存储的 数据大小来设定的，作者建议如果存储很多小对象，page 大小最好设置为 32 或者 64bytes；如果存储很大大对象，则可以使用更大的 page，如果不确定，就使用默认值|
|vm-pages 134217728|	设置 swap 文件中的 page 数量，由于页表（一种表示页面空闲或使用的 bitmap）是在放在内存中的，，在磁盘上每 8 个 pages 将消耗 1byte 的内存。|
|vm-max-threads 4|	设置访问swap文件的线程数,最好不要超过机器的核数,如果设置为0,那么所有对swap文件的操作都是串行的，可能会造成比较长时间的延迟。默认值为4|
|glueoutputbuf yes|	设置在向客户端应答时，是否把较小的包合并为一个包发送，默认为开启|
|hash-max-zipmap-entries 64<br/>hash-max-zipmap-value 512	|指定在超过一定的数量或者最大的元素超过某一临界值时，采用一种特殊的哈希算法|
|activerehashing yes|	指定是否激活重置哈希，默认为开启（后面在介绍 Redis 的哈希算法时具体介绍）|
|include /path/to/local.conf|	指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件|

### 具体属性解析

#### requirepass foobared

##### 始化Redis密码：

在配置文件中有个参数： requirepass  这个就是配置redis访问密码的参数；

比如 requirepass test123；（Ps:需重启Redis才能生效）

redis的查询速度是非常快的，外部用户一秒内可以尝试多大150K个密码；所以密码要尽量长（对于DBA 没有必要必须记住密码）；

##### 不重启Redis设置密码：

在配置文件中配置requirepass的密码（当redis重启时密码依然有效）。

	redis 127.0.0.1:6379> config set requirepass test123
	查询密码：
		redis 127.0.0.1:6379> config get requirepass
		(error) ERR operation not permitted
	密码验证：
		redis 127.0.0.1:6379> auth test123
		OK

##### 登陆有密码的Redis：

在登录的时候的时候输入密码： 
	
	redis-cli -p 6379 -a test123