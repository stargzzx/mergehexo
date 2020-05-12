---
title: jenkins | 使用镜像加速安装插件
date: 2020-03-03 16:49:31
categories:
- 中间件
- jenkins
- 基础
tags:
- jenkins
---
我因为没有使用镜像加速，导致在入门的安装镜像的时候失败，安装了一天了，最后很多都没安装成功。

这一篇我将介绍如何利用镜像加速。

<!-- more -->

<br/>

# 背景

<br/>

MacBook

<br/>

# 卸载 jenkins

<br/>

因为，我不断的调用重启

	http://localhost:8080/restart

不知道处于什么原因，让我的界面进去了（入门的插件安装没有成功进去了，但是，进去后，我并不知道如何再次安装那些插件，所以，我想将其卸载了，重新安装）

这里就要说明一下，如何完整的删除掉 jenkins

首先是关掉 jenkins 服务

	launchctl unload ~/Library/LaunchAgents/homebrew.mxcl.jenkins.plist

我因为使用的是 brew 安装的，所以，卸载很简单

	brew uninstall jenkins

这里仅仅只是卸载了软件，没有删除配置文件。

在 MacBook 中配置文件的目录如下（以我个人举例）：

	/Users/licong/.jenkins

所以，我们要执行

	rm -rf .jenkins

这样我们就已经卸载干净了。

<br/>

# 重新安装

<br/>

直接执行

	brew install jenkins

关于安装步骤你可以看我的上一篇博文。

[jenkins | 简介和安装](https://benpaodewoniu.github.io/2020/03/03/jenkins0/)

这里假设我们现在又重新安装了一遍，如果你正确卸载了，那么你访问

	http://localhost:8080/

这个应该是，下面的这个界面

![](/images/jenkins/1_0.png)

如果你没有办法穿过墙或者没有配置代理，那么，你会停留在这个界面很长时间。

<br/>

# 配置代理

<br/>

[jenkins 存在的代理](http://mirrors.jenkins-ci.org/status.html)

我们的配置文件在

	/Users/licong/.jenkins

首先，更改站点

	cd /Users/licong/.jenkins

然后

	vim hudson.model.UpdateCenter.xml

将 http://updates.jenkins-ci.org/update-center.json 改成 https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

这个文件的全部内容如下：

	<?xml version='1.1' encoding='UTF-8'?>
	<sites>
	  <site>
	    <id>default</id>
	    <url>https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json</url>
	  </site>
	</sites>

这里改的地方，相当于，目前网上流行的那个更新站点的地方

![](/images/jenkins/1_1.png)

但是，改到这里依然不行，我们还需要更改

	/Users/licong/.jenkins/updates/default.json

[参考资料](https://www.cnblogs.com/hellxz/p/jenkins_install_plugins_faster.html)

![](/images/jenkins/1_2.png)

这里我们发现，每个插件下载路径依旧没有改变，变的只是这个json是从清华源下来的，其内写死的插件下载地址是没有变的，还是从官网下载！

所以无论是更换还是没更换镜像json，下载插件的速度其实是没有变的！这真是令人心痛！

使用 vim 编辑文件，如下，替换所有插件下载的url

	vim /Users/licong/.jenkins/updates/default.json

执行

	:1,$s/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g

替换连接测试url

	:1,$s/http:\/\/www.google.com/https:\/\/www.baidu.com/g

进入vi先输入 : 然后再粘贴上边的 : 后边的命令，注意不要写两个冒号！

修改完成保存退出:wq

重启Jenkins

	http://localhost:8080/restart

按照提示，一直出现

![](/images/jenkins/0_0.png)

见证奇迹，妈妈再也不用担心插件安装不上了。

![](/images/jenkins/1_3.png)

后面就跟着提示来就可以了。

最后，我配置完 password 等

在

	http://localhost:8080/manage

发现

![](/images/jenkins/1_4.png)

询问运维小哥后，他说出现这个正常，应该是版本问题，不需要管。（因为我点击 「纠正」 并没有出现安装插件）

ok，代理设置在这里就要结束了，感谢观看。

<br/>

# 写在最后

<br/>

按理说经过上面的配置，我安装确实是快了很多，但是，有一点很玄学的是，我在看

![](/images/jenkins/0_0.png)

安装的过程中很慢，于是，我想看看是不是代理换了，于是我主动输入

	http://localhost:8080/manage

然后点击到

	http://localhost:8080/pluginManager/advanced

发现，确实变成了

![](/images/jenkins/1_1.png)

于是，我又退回（退回后，貌似它会自己重启，我忘记了）

	http://localhost:8080/

这期间应该什么都没做，然后就发现其下载如飞了。

我认为出现这个的原因可以如下：

1. 可能本身就已经下载如飞了，只不过我太急了
2. 玄学～～～