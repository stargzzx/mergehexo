---
title: mitmproxy的用法
date: 2019-07-08 21:15:57
categories:
- spider
tags:
- spider
- 爬虫
- 手机爬虫
- mitmproxy
---
这个是让你学会使用mitmproxy。

<!--more-->

# 参考资料

[mitmproxy的使用](https://edu.csdn.net/notebook/python/week11/2.html)

[mitmdump抓取数据的使用步骤实例——第一阶段](https://blog.csdn.net/u014796999/article/details/83934512)

[Python3+mitmproxy安装使用教程](https://www.cnblogs.com/lsdb/p/10106655.html)

[深入了解mitmproxy](https://www.jianshu.com/p/a495cc016682)

# python编写脚本

这里我使用的是 anaconda 的集成虚拟环境，更加值得注意的是，mitmproxy的使用python 版本至少是 3.6。

进入 python 3.6 的虚拟环境。

输入

	pip install mitmproxy
	
这个一定要用3.6以上的环境安装。

## 写脚本

写出来的脚本，运行下面的命令

	mitmdump.exe -s example_script.py
	
在虚拟环境下，运行。

![](/images/spider/6_0.png)

我们在相关的python文件中写对应的代码，然后处理。和正常的python代码一样，只不过需要有两个注意点。

{% codeblock %}
from mitmproxy import ctx

# 所有发出的请求数据包都会被这个方法所处理
# 所谓的处理，我们这里只是打印一下一些项；当然可以修改这些项的值直接给这些项赋值即可
def request(flow):
    # 获取请求对象
    request = flow.request
    # 实例化输出类
    info = ctx.log.info
    # 打印请求的url
    info(request.url)
    # 打印请求方法
    info(request.method)
    # 打印host头
    info(request.host)
    # 打印请求端口
    info(str(request.port))
    # 打印所有请求头部
    info(str(request.headers))
    # 打印cookie头
    info(str(request.cookies))

# 所有服务器响应的数据包都会被这个方法处理
# 所谓的处理，我们这里只是打印一下一些项
def response(flow):
    # 获取响应对象
    response = flow.response
    # 实例化输出类
    info = ctx.log.info
    # 打印响应码
    info(str(response.status_code))
    # 打印所有头部
    info(str(response.headers))
    # 打印cookie头部
    info(str(response.cookies))
    # 打印响应报文内容
    info(str(response.text))
{% endcodeblock %}

主要events一览表

需要修改各种事件内容时，重写以下对应方法，这里主要用的是request、response方法

{% codeblock %}
import typing

import mitmproxy.addonmanager
import mitmproxy.connections
import mitmproxy.http
import mitmproxy.log
import mitmproxy.tcp
import mitmproxy.websocket
import mitmproxy.proxy.protocol


class Events:
    # HTTP lifecycle
    def http_connect(self, flow: mitmproxy.http.HTTPFlow):
        """
            An HTTP CONNECT request was received. Setting a non 2xx response on
            the flow will return the response to the client abort the
            connection. CONNECT requests and responses do not generate the usual
            HTTP handler events. CONNECT requests are only valid in regular and
            upstream proxy modes.
        """

    def requestheaders(self, flow: mitmproxy.http.HTTPFlow):
        """
            HTTP request headers were successfully read. At this point, the body
            is empty.
        """

    def request(self, flow: mitmproxy.http.HTTPFlow):
        """
            The full HTTP request has been read.
        """

    def responseheaders(self, flow: mitmproxy.http.HTTPFlow):
        """
            HTTP response headers were successfully read. At this point, the body
            is empty.
        """

    def response(self, flow: mitmproxy.http.HTTPFlow):
        """
            The full HTTP response has been read.
        """

    def error(self, flow: mitmproxy.http.HTTPFlow):
        """
            An HTTP error has occurred, e.g. invalid server responses, or
            interrupted connections. This is distinct from a valid server HTTP
            error response, which is simply a response with an HTTP error code.
        """

    # TCP lifecycle
    def tcp_start(self, flow: mitmproxy.tcp.TCPFlow):
        """
            A TCP connection has started.
        """

    def tcp_message(self, flow: mitmproxy.tcp.TCPFlow):
        """
            A TCP connection has received a message. The most recent message
            will be flow.messages[-1]. The message is user-modifiable.
        """

    def tcp_error(self, flow: mitmproxy.tcp.TCPFlow):
        """
            A TCP error has occurred.
        """

    def tcp_end(self, flow: mitmproxy.tcp.TCPFlow):
        """
            A TCP connection has ended.
        """

    # Websocket lifecycle
    def websocket_handshake(self, flow: mitmproxy.http.HTTPFlow):
        """
            Called when a client wants to establish a WebSocket connection. The
            WebSocket-specific headers can be manipulated to alter the
            handshake. The flow object is guaranteed to have a non-None request
            attribute.
        """

    def websocket_start(self, flow: mitmproxy.websocket.WebSocketFlow):
        """
            A websocket connection has commenced.
        """

    def websocket_message(self, flow: mitmproxy.websocket.WebSocketFlow):
        """
            Called when a WebSocket message is received from the client or
            server. The most recent message will be flow.messages[-1]. The
            message is user-modifiable. Currently there are two types of
            messages, corresponding to the BINARY and TEXT frame types.
        """

    def websocket_error(self, flow: mitmproxy.websocket.WebSocketFlow):
        """
            A websocket connection has had an error.
        """

    def websocket_end(self, flow: mitmproxy.websocket.WebSocketFlow):
        """
            A websocket connection has ended.
        """

    # Network lifecycle
    def clientconnect(self, layer: mitmproxy.proxy.protocol.Layer):
        """
            A client has connected to mitmproxy. Note that a connection can
            correspond to multiple HTTP requests.
        """

    def clientdisconnect(self, layer: mitmproxy.proxy.protocol.Layer):
        """
            A client has disconnected from mitmproxy.
        """

    def serverconnect(self, conn: mitmproxy.connections.ServerConnection):
        """
            Mitmproxy has connected to a server. Note that a connection can
            correspond to multiple requests.
        """

    def serverdisconnect(self, conn: mitmproxy.connections.ServerConnection):
        """
            Mitmproxy has disconnected from a server.
        """

    def next_layer(self, layer: mitmproxy.proxy.protocol.Layer):
        """
            Network layers are being switched. You may change which layer will
            be used by returning a new layer object from this event.
        """

    # General lifecycle
    def configure(self, updated: typing.Set[str]):
        """
            Called when configuration changes. The updated argument is a
            set-like object containing the keys of all changed options. This
            event is called during startup with all options in the updated set.
        """

    def done(self):
        """
            Called when the addon shuts down, either by being removed from
            the mitmproxy instance, or when mitmproxy itself shuts down. On
            shutdown, this event is called after the event loop is
            terminated, guaranteeing that it will be the final event an addon
            sees. Note that log handlers are shut down at this point, so
            calls to log functions will produce no output.
        """

    def load(self, entry: mitmproxy.addonmanager.Loader):
        """
            Called when an addon is first loaded. This event receives a Loader
            object, which contains methods for adding options and commands. This
            method is where the addon configures itself.
        """

    def log(self, entry: mitmproxy.log.LogEntry):
        """
            Called whenever a new log entry is created through the mitmproxy
            context. Be careful not to log from this event, which will cause an
            infinite loop!
        """

    def running(self):
        """
            Called when the proxy is completely up and running. At this point,
            you can expect the proxy to be bound to a port, and all addons to be
            loaded.
        """

    def update(self, flows: typing.Sequence[mitmproxy.flow.Flow]):
        """
            Update is called when one or more flow objects have been modified,
            usually from a different addon.
        """
{% endcodeblock %}

针对http，常用的API

{% codeblock %}
#http.HTTPFlow 实例 flow
flow.request.headers #获取所有头信息，包含Host、User-Agent、Content-type等字段
flow.request.url #完整的请求地址，包含域名及请求参数，但是不包含放在body里面的请求参数
flow.request.pretty_url #同flow.request.url目前没看出什么差别
flow.request.host #域名
flow.request.method #请求方式。POST、GET等
flow.request.scheme #什么请求 ，如https
flow.request.path # 请求的路径，url除域名之外的内容
flow.request.get_text() #请求中body内容，有一些http会把请求参数放在body里面，那么可通过此方法获取，返回字典类型
flow.request.query #返回MultiDictView类型的数据，url直接带的键值参数
flow.request.get_content()#bytes,结果如flow.request.get_text() 
flow.request.raw_content #bytes,结果如flow.request.get_content()
flow.request.urlencoded_form #MultiDictView，content-type：application/x-www-form-urlencoded时的请求参数，不包含url直接带的键值参数
flow.request.multipart_form #MultiDictView，content-type：multipart/form-data
时的请求参数，不包含url直接带的键值参数
#以上均为获取request信息的一些常用方法，对于response，同理
flow.response.status_code #状态码
flow.response.text#返回内容，已解码
flow.response.content #返回内容，二进制
flow.response.setText()#修改返回内容，不需要转码
#以上为不完全列举
{% endcodeblock %}

# 一个例子

{% codeblock %}
#修改response内容，这里是服务器已经有返回了结果，再更改，也可以做不经过服务器处理，直接返回，看需求
def response(flow:http.HTTPFlow)-> None:
    #特定接口需要返回1001结果
    interface_list=["page/**"] #由于涉及公司隐私问题，隐藏实际的接口
    
    url_path=flow.request.path
    if  url_path.split("?")[0] in  interface_list:
        ctx.log.info("#"*50)
        ctx.log.info("待修改路径的内容："+url_path)
        ctx.log.info("修改成：1001错误返回")
        ctx.log.info("修改前：\n")
        ctx.log.info(flow.response.text)
        flow.response.set_text(json.dumps({"result":"1001","message":"服务异常"}))#修改，使用set_text不用转码
        ctx.log.info("修改后：\n")
        ctx.log.info(flow.response.text)
        ctx.log.info("#"*50)
    elif  flow.request.host in  host_list:#host_list 域名列表，作为全局变量，公司有多个域名，也隐藏
        ctx.log.info("response= "+flow.response.text)
{% endcodeblock %}

















