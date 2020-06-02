---
title: 软件 | shadowsocks 在阿里云「香港」服务器搭建
date: 2020-04-24 20:53:59
categories:
- software
- 电脑软件
tags:
- software
---
首先，我坚决拥护党和国家的政策，墙的存在是现代社会中必须存在的事物。但是，有的时候科研和程序员对于外面信息的获取还是刚需的。

为了让有志青年尽早的获取最新的信息，所以，搞了这个搭建教程。希望大家不忘初心，为了民族伟大复兴而努力。

<!-- more -->

<br/>

# 购买阿里云

<br/>

我们必须购买一个境外服务器，在这里我选择了香港服务器。

我的配置是最低的，但是，我搭建完使用的时候，发现看 1080p 的 YouTube 非常流畅。

我的配置如下

- ubuntu 16.04
- 1Mbit 带宽
- 有外部 IP

因为没有任何优惠，而且，情况又比较紧急，所以，购买了，大概「79.15」。如果，你是双十一购买的话，或许 3 年才 299 元，真的是非常实惠了。。。

不过，这次也不是我交钱。

有一点要注意的是，国内的服务器需要备案，但是，国外的服务器购买完之后就可以直接用。

<br/>

# 卸载阿里云盾（安骑士）服务

<br/>

由于阿里会检测服务器是否存在ss，如果保留安骑士，阿里云会给你发邮件警告。

如果你是首次购买服务器，请在购买时直接反选安骑士服务。

如果已经安装了安骑士，那么我们需要使用以下命令移除：

	wget http://update.aegis.aliyun.com/download/uninstall.sh
	chmod +x uninstall.sh
	./uninstall.sh

	wget http://update.aegis.aliyun.com/download/quartz_uninstall.sh
	chmod +x quartz_uninstall.sh
	./quartz_uninstall.sh

	pkill aliyun-service
	rm -rf /etc/init.d/agentwatch /usr/sbin/aliyun-service /usr/local/aegis*
	rm uninstall.sh
	rm quartz_uninstall.sh

<br/>

# 配置过程

<br/>

我们的系统中，初始安装了 py2.7 和 py3.5 ，我们用的是 2.7.

ShadowSocks的安装包在GitHub上有很多版本，我这里选择的是通过Python的包管理工具pip直接自动安装。

	curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py"  //下载pip安装包
	python get-pip.py //安装pip
	pip install shadowsocks //安装shadowsocks

这3个命令执行完，服务端实际已经安装完毕了，没错，安装就是这么简单，搭代理没想的那么麻烦，下面还差的就是配置了。

	vim /etc/shadowsocks.json

我们就编写这个文件。

	{
	"server":"12.2.2.2",//如果是阿里云专有网络，切记配的是私有IP不是公有IP，此处是大坑
	"server_port": 16637,//服务端监听端口号 
	"password":"abc",//客户端访问密码
	"timeout": 600,//超时时间
	"method": "aes-256-cfb"//加密方式，有多种可选，具体不赘述，默认256加密已经够用
	}

上面的配置就已经足够了，网上还有其他的配置，但是，配置了多余的，有可能会出错。

如果，你对其他的参数实在是感兴趣，可以看一下下面的链接。

- [使用shadowsocks科学上网](https://www.textarea.com/ExpectoPatronum/shiyong-shadowsocks-kexue-shangwang-265/)

这个时候基本上 `shadowsocks` 已经配置好了。但是，对于阿里云我们还需要做两个步骤。

- 关闭服务器端口防火墙
- 打开服务器端口的安全组

## 关闭服务器端口防火墙

	iptables -A INPUT -p tcp --dport 16637 -j ACCEPT //设置防火墙规则，允许端口进入
	iptables -F   //flush规则，使规则生效
	ssserver -c /etc/shadowsocks.json --log-file /home/log -d start  //启动shadowsocks,输出日志可在/home/log中查看
	netstat -apn | grep 16637  //确认1080监听端口已启动，如果没有，查看/home/log日志文件，确认错误原因，一般原因是shadowsocks.json配置错误，对照错误信息修改即可

## 打开服务器的安全组

阿里云还有一个外部的安全组规则。

登录阿里云控制台，在云服务器ECS->实例->管理->本实例安全组->安全组列表->配置规则->添加安全组规则中，允许1080端口进和出，阿里云界面交互很棒的，一看就会操作。

里面有一个入方向和出方向。

![](/images/software/6_0.png)

登录大陆地区的服务器，执行 `telnet 你的公网ip地址  16637`，如果现实connected即代表服务端已搭建成功，否则请检查防火墙和阿里云安全组是否已开放1080端口

这个时候我们就能愉快的进行学习了。

<br/>

# 下载客户端

<br/>

你可以通过下面的链接下载客户端。

- [客户端下载](https://shadowsocks.org/en/download/clients.html)

配置信息如下：

- 服务器地址配置你的阿里云公网 ip
- 服务器端口配置 16637 ，也就是 shadowsocks.json 里配置的 server_port 对应值
- 密码配置 shadowsocks.json 里对应的 password 值
- 加密方式配置和 shadowsocks.json 里的 method 对应值一样即可
- 代理端口使用默认值即可，但是请记住代理端口，该端口需要在浏览器配置代理时使用

<br/>

# 被封了怎么办

<br/>

如果不幸被封了，那么，要不要慌，只需要换一个端口就好了。

修改下面的信息

- `/etc/shadowsocks.json` 里面的 `server_port`
- 防火墙开启这个端口
- 安全组规则也添加这个端口「可以添加一个范围的端口，这样就不需要老师弄这个安全组规则了」

然后执行

	ssserver -c /etc/shadowsocks.json --log-file /home/log -d restart

<br/>

# 再次升级

<br/>

在更换了几次端口号之后，我的服务器被阿里给封了，远程也登不上，所以，我了解到了一个新的技术。 `V2ray`.

但是，我还没开始尝试，我现在先把链接放一下。

- [Vpn/ss/ssr与V2Ray的介绍与区别](https://www.imdupeng.cn/server/guid-diff-vpn-ss-ssr-and-v2ray.html)
- [GFW BLOG](http://www.chinagfw.org/2016/08/shadowsocks_31.html)
- [个人翻墙事宜的一些总结与经验分享](https://medium.com/@vonsis/%E8%BF%91%E6%9C%9F%E4%BB%A5%E6%9D%A5%E7%A7%91%E5%AD%A6%E4%B8%8A%E7%BD%91-%E7%BF%BB%E5%A2%99-%E4%BA%8B%E5%AE%9C%E7%9A%84%E4%B8%80%E4%BA%9B%E6%80%BB%E7%BB%93-8928d55bce73)

# 参考资料

<br/>

- [科学上网相关知识总结](https://crifan.github.io/scientific_network_summary/website/)
- [阿里云ShadowSocks代理搭建](http://syhong.net/2018/07/19/shadowSocks/)
- [客户端下载](https://shadowsocks.org/en/download/clients.html)
- [阿里云服务器搭建Shadowsocks Server及使用SwitchyOmega切换代理设置实战教程](https://blog.sbot.io/articles/36/%E9%98%BF%E9%87%8C%E4%BA%91%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%90%AD%E5%BB%BAShadowsocks-Server%E5%8F%8A%E4%BD%BF%E7%94%A8SwitchyOmega%E5%88%87%E6%8D%A2%E4%BB%A3%E7%90%86%E8%AE%BE%E7%BD%AE%E5%AE%9E%E6%88%98%E6%95%99%E7%A8%8B)
- [阿里云服务器怎么开启或关闭8080端口？](https://yq.aliyun.com/articles/225815)


