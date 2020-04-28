---
title: 开源项目 | proxy_pool 更改验证 IP 有效的方式
date: 2020-04-28 12:45:34
categories:
- 开源项目
- python
- proxy_pool
- 基础
tags:
- 开源项目
---
我在下面三个环境中运行了该项目

- 本地 MacBook
- 服务器 宿主机
- 服务器 docker

<!-- more -->

验证的代码在

	proxy_pool/Util/utilFunction.py validUsefulProxy()

这个方法中。


<br/>

# 本地和宿主机

<br/>

业务需求是在 docker 中跑，我在宿主机和本地跑的时候都通过了。

也就是访问

	ip:5010/get_all

有很多数据可以查到。

并且，我在这两处跑的时候，并没有修改源代码，在获取数据的时候，确实获取了大量的代理 IP，但是，在获取的这些代理 IP 中有大部分都不可用。

也就是，尽管我修改了 `validUsefulProxy` 这个方法中的验证 `URL` ，但是，最后得到的代理 IP，却极大部分不能用，造成这方面的原因我认为主要有以下几点

- `validUsefulProxy` 这个方法部分代码写的不完善
- 代理IP 的时效性太低
- 不知名的原因

<br/>

# docker

<br/>

在 docker 中跑的时候并不顺利，首先，我们先查看源代码

```python
def validUsefulProxy(proxy):
    """
    检验代理是否可用
    :param proxy:
    :return:
    """
    if isinstance(proxy, bytes):
        proxy = proxy.decode("utf8")
    proxies = {"http": "http://{proxy}".format(proxy=proxy)}
    try:
        r = requests.get('https://data.eosbeijing.one/api/listBPByEOS?chain=eos', proxies=proxies, timeout=10, verify=False)
        if r.status_code == 200:
            return True
    except Exception as e:
        pass
    return False
```

ps：里面的 `url` 已经换成我将要访问的 `url` 了。

## 缺少 https

	proxies = {"http": "http://{proxy}".format(proxy=proxy)}

上面改成

	proxies = {"http": f"http://{proxy}","https": f"http://{proxy}"}

## InsecureRequestWarning 错误

主要是由

	request.get(url,verify=False) 的 verify=False 导致的

原因如下

>requests 库其实是基于 urllib 编写的，对 urllib 进行了封装，使得使用时候的体验好了很多，现在 urllib 已经出到了3版本，功能和性能自然是提升了不少。
所以，requests最新版本也是基于最新的 urllib3 进行封装。

>在urllib2时代对https的处理非常简单，只需要在请求的时候加上 verify=False 即可，这个参数的意思是忽略https安全证书的验证，也就是不验证证书的可靠性，直接请求，
这其实是不安全的，因为证书可以伪造，不验证的话就不能保证数据的真实性。

>在urllib3时代，官方强制验证https的安全证书，如果没有通过是不能通过请求的，虽然添加忽略验证的参数，但是依然会 给出醒目的 Warning，这一点没毛病。

接下来， 禁用 urllib3 ，然后在相关文件上添加

	import urllib3
	urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)

最后，我将这个方法改成了下面的模样

```python
def validUsefulProxy(proxy):
    """
    检验代理是否可用
    :param proxy:
    :return:
    """
    if isinstance(proxy, bytes):
        proxy = proxy.decode("utf8")
    proxies = {"http": f"http://{proxy}","https":f"http://{proxy}"}
    try:
        r = requests.get('https://data.eosbeijing.one/api/listBPByEOS?chain=eos', proxies=proxies, timeout=30)
        if r.status_code == 200:
            return True
    except Exception as e:
        pass
    return False
```

最后，确实获得了几个 代理IP，为了验证这些 IP 可用，我写了下面的验证脚本

```python
import requests
import urllib3
import argparse
urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)


def validUsefulProxy(proxy):
    proxies = {"http": f"http://{proxy}", "https": f"http://{proxy}"}
    r = None
    try:
        r = requests.get('https://data.eosbeijing.one/api/listBPByEOS?chain=eos', proxies=proxies, timeout=10)
        print(3)
        if r.status_code == 200:
            print(1)
            return True
    except Exception as e:
        print(e)
    print(r)
    return False

parser = argparse.ArgumentParser()
parser.add_argument("--ip")
args = parser.parse_args()
print(args.ip)
validUsefulProxy(args.ip)
```

然后

	python test.py --ip ****

检验通过

<br/>

# 总结

<br/>

- 我的 `docker` 和 `宿主机` 是 `host` 连接方式
- `docker` 环境 `ubuntu/python3.7`
