---
title: zookeeper | python 读取远程 zookeeper 配置
date: 2020-03-23 17:44:22
categories:
- zookeeper
tags:
- zookeeper
---
使用 python 来操作和对接远程 zookeeper。

用的是 zookeeper 3.4.14.

环境是 MacBook。语言是 python。

这这篇博文中，我会举一个我在项目中具体使用的例子。

<!-- more -->

<br/>

# 参考资料

<br/>

[Python：kazoo模块与Zookeeper交互](https://blog.csdn.net/mouday/article/details/91975169)
[zookeeper应用场景之配置文件同步](https://www.tuicool.com/articles/nYFzEj)
[Zookeeper运维的一些经验](https://www.cnblogs.com/seaspring/p/6099750.html)
[python操作zookeeper](https://zhuanlan.zhihu.com/p/34177172)

<br/>

# python 编写

<br/>

## 安装

	pip install kazoo

## 部分代码展示

```python
>>> from kazoo.client import KazooClient

>>> zk = KazooClient(hosts='127.0.0.1:2181')

# zkCli.sh 订阅
>>> zk.start()

# ls /
>>> zk.get_children("/")
['zookeeper']

# create /mydata data
>>> zk.create("/mydata", "data".encode())
'/mydata'

# get /mydata
>>> zk.get("/mydata")
(b'data',
 ZnodeStat(czxid=40, mzxid=40, ctime=1560494490063, mtime=1560494490063, version=0, cversion=0, 
 aversion=0, ephemeralOwner=0, dataLength=4, numChildren=0, pzxid=40))

# set /mydata data2
>>> zk.set("/mydata", "data2".encode())
ZnodeStat(czxid=40, mzxid=41, ctime=1560494490063, mtime=1560494514269, version=1, cversion=0, 
aversion=0, ephemeralOwner=0, dataLength=5, numChildren=0, pzxid=40)

# delete /mydata
>>> zk.delete("/mydata")
True

# 判断节点是否存在
>>> zk.exists("/mydata")

# quit
>>> zk.stop()
```

## 监控代码

```python
# -*- coding: utf-8 -*-

import time

from kazoo.client import KazooClient

zk = KazooClient(hosts='127.0.0.1:2181')

# 启动
zk.start()


@zk.DataWatch("/mydata")
def watch_children(data, stat):
    print("Data is %s" % data)
    if data:
        print("Version is %s" % stat.version)


while True:
    time.sleep(10)
```

## 项目中的实例


### 写入 zookeeper

其实，一般保存到 zookeeper 的都是希望可以很容易解析出来的数据结构，比如 json.

但是，自己写 json 很容易写坏，所以，我的建议是，自己写一个数据结构，比如

	dict = {
		'test':'txt'
	}

然后，使用 data = json.dumps(str(dict)) ，得到封装好的数据，然后，把这个数据进行编写成字节码，就得到了，我们要保存的数据

	data = data.encode()

### 读取 zookeeper

假如我们要进行解码，就可以按照先解码

	data = data.decode()

然后，json 解析

	data = json.loads(data)

但是，在解析或者使用解析数据的时候，可能会有很大的问题。

下面将问题每一个都分析一下。

#### \\n 问题

如果原始数据中的 \\n 是换行的意思，那么经过解码之后，读出来的数据就真的只是 \\n 了。

比如 

	data = "123\n123"
	print(len(data))
		# 7 其中 \n 算作一个
	经过编码写入然后读取出来之后
	print(len(data))
		# 10 其中两头的 " " 算作两个，\n 也算作两个字符

所以，我们要把解码出来的数据进行 \\n 替换。

	data[1:-1].replace(r'\n','\n')

注意，一定要使用 r 作为修饰。[1:-1] 是为了去掉 \" 这个字符。

#### \\\' 问题

我们解析出来的 str 可能是下面的内容

	'{\'mysql_host\': \'127.0.0.1\', \'mysql_port\': 3306, \'mysql_password\': \'1\', \'mysql_database\': \'ant\', \'mysql_user\': \'root\', \'kafka_host\': \'127.0.0.1\', \'kafka_port\': 9092, \'proxy_host\': \'127.0.0.1\', \'proxy_port\': 1087}'

这样的话，json.loads 是解析不了的，需要把 \\\' 换成 \"

也就是

	content = content.replace('\'', '"')

其完整的例子如下

```python
from kazoo.client import KazooClient, KazooState
from ant.config.setting import SETTINGS
import json
from ant.util.utility import encrypt, decrypt


class ZookeeperConfig:
    __instance = None

    def __init__(self, host, port):
        self.setting = None
        self.data = None
        self.zk = KazooClient(
            hosts=f'{host}:{port}'
            , timeout=10.0  # 连接超时时间
        )
        if ZookeeperConfig.__instance:
            self.get_instance()
        self.init()

    # 实际的对象创建发生在调用get_instance的时候
    @classmethod
    def get_instance(cls, *args):
        if not cls.__instance:
            cls.__instance = ZookeeperConfig(*args)
        return cls.__instance

    def init(self):
        self.zk.start()

    def get_content(self, node):
        data, stat = self.zk.get(node)
        msg = data.decode()
        return json.loads(msg)

    def get_encrypt_content(self, node):
        encrypt_content, stat = self.zk.get(node)
        msg = encrypt_content.decode()
        msg = msg[1:-1].replace(r'\n', '\n')
        content = decrypt(SETTINGS["ant.private_key"], msg)
        content = content.replace('\'', '"')
        return json.loads(content)

    def set_data(self, node, data):
        content = json.dumps(str(data)).encode()
        self.write(node, content)

    def create_node(self, node):
        if not self.exist_node(node):
            self.zk.create(node, "data".encode())

    def exist_node(self, node):
        return self.zk.exists(node)

    def write(self, node, data):
        self.zk.set(node, data)

    def write_encrypt_content(self, node, content):
        key_private = SETTINGS["ant.private_key"]
        content = encrypt(key_private, str(content))
        self.write_long_path_node(node, content)

    def write_long_path_node(self, long_node, data):
        path_node = long_node.split('/')[1:]
        for i in range(len(path_node)):
            path = '/' + '/'.join(path_node[0:i + 1])
            self.create_node(path)
        self.set_data(long_node, data)


if __name__ == '__main__':
    data = {
        "mysql_host": "127.0.0.1",
        "mysql_port": 3306,
        "mysql_password": "***",
        "mysql_database": "***",
        "mysql_user": "root",
        "kafka_host": "127.0.0.1",
        "kafka_port": 9092,
        "proxy_host": "127.0.0.1",
        "proxy_port": 1087
    }
    key_secret = {
        "**": {
            "key": "riWqGZn62xR3m79Nwuum8g==",
            "secret": "riWqGZn62xR3m79Nwuum8g=="
        }
    }
    z = ZookeeperConfig.get_instance('IP', '2181')
    z.write_encrypt_content('/ant/ant/key_secret', key_secret)
    z.get_encrypt_content('/ant/ant/config')
```

需要提示的是，我在写入数据的时候用的是 \" ，如果你想要使用这个例子来解析的话，最好也使用 \" .

ps:

在 zookpper 中，敏感数据最好加密

在这份代码中，我的加密/解密函数如下

	encrypt
	decrypt

good lucky!!!