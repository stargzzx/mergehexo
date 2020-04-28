---
title: 开源项目 | proxy_pool 安装和运行
date: 2020-04-21 18:28:33
categories:
- 开源项目
- python
- proxy_pool
- 基础
tags:
- 开源项目
---
这个项目是做 代理IP 池的。

对于，一些爬虫项目是刚需。

[项目地址](https://github.com/jhao104/proxy_pool)

<!-- more -->

<br/>

# 项目背景

<br/>

- python3.7
- redis
- MacBook

你可以在我下面的栏目中了解，什么是 redis 以及如何用。

[redis](https://benpaodewoniu.github.io/categories/redis/)

<br/>

# 安装和使用

<br/>

## 安装

	git clone git@github.com:jhao104/proxy_pool.git

## 安装依赖

	我使用了 conda 新创建了一个虚拟环境 py3.7
	pip install -i https://pypi.tuna.tsinghua.edu.cn/simple -r requirements.txt

## 配置

- Config/setting.py

```python
DATABASES = {
    "default": {
        "TYPE": "REDIS",        # 目前支持SSDB或REDIS数据库
        "HOST": "127.0.0.1",   # db host
        "PORT": 6379,          # db port，例如SSDB通常使用8888，REDIS通常默认使用6379
        "NAME": "proxy",       # 默认配置
        "PASSWORD": ""         # db password REDIS 默认没有密码

    }
}


# 配置 ProxyGetter

PROXY_GETTER = [
    "freeProxy01",      # 这里是启用的代理抓取函数名，可在ProxyGetter/getFreeProxy.py 扩展
    "freeProxy02",
    ....
]


# 配置 API服务

SERVER_API = {
    "HOST": "0.0.0.0",  # 监听ip, 0.0.0.0 监听所有IP
    "PORT": 5010        # 监听端口
}
       
# 上面配置启动后，代理池访问地址为 http://127.0.0.1:5010
```

## 启动

进入 cli目录下通过ProxyPool.py启动

	cd cli

程序分为: 

- schedule 调度程序
- webserver Api服务

<br/>

	首先启动调度程序
	python proxyPool.py schedule
	然后启动webApi服务
	python proxyPool.py webserver

这个时候，项目就已经开始爬取代理 IP 了。

## 使用

启动过几分钟后就能看到抓取到的代理IP，你可以直接到数据库中查看，推荐一个SSDB可视化工具。

也可以通过api访问 http://127.0.0.1:5010 查看。

|api|	method|	Description	arg|
|---|---|---|
|/|	GET	api介绍|	None|
|/get|	GET	随机获取一个代理|	None|
|/get_all|	GET	获取所有代理|	None|
|/get_status|	GET	查看代理数量|	None|
/delete|	GET	删除代理|	proxy=host:ip|

## 爬虫使用

如果要在爬虫代码中使用的话， 可以将此api封装成函数直接使用，例如：

```python
import requests

def get_proxy():
    return requests.get("http://127.0.0.1:5010/get/").json()

def delete_proxy(proxy):
    requests.get("http://127.0.0.1:5010/delete/?proxy={}".format(proxy))

# your spider code

def getHtml():
    # ....
    retry_count = 5
    proxy = get_proxy().get("proxy")
    while retry_count > 0:
        try:
            html = requests.get('http://www.example.com', proxies={"http": "http://{}".format(proxy)})
            # 使用代理访问
            return html
        except Exception:
            retry_count -= 1
    # 出错5次, 删除代理池中代理
    delete_proxy(proxy)
    return None
```

在项目的 github 中有更深层次的进阶使用，可以去看一下

[项目地址](https://github.com/jhao104/proxy_pool)