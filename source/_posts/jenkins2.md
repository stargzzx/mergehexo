---
title: jenkins | 向GitHub提交代码时触发Jenkins自动构建
date: 2020-03-09 15:02:58
categories:
- jenkins
tags:
- jenkins
---
这个是公司交给我的任务之一。

在这个教程里，我将梳理一下整个流程。

<!-- more -->

<br/>

# 参考资料

<br/>

[配置GitHub Push自动触发Jenkins的构建](https://www.jianshu.com/p/f90013658c38)
[实战：向GitHub提交代码时触发Jenkins自动构建](https://blog.csdn.net/boling_cavalry/article/details/78943061?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task)

<br/>

# 原理解析

<br/>

这个解析非官方，是我在部署过程中的总结。

我们将 code push 到 github 的时候，需要 github 通知 jenkins 拉取代码，重新部署，所以需要有一个通信手段（所以部署 jenkins 的机器需要外部网络可以访问的 ip）。

这个通信手段在 github 上，叫做

- Webhooks

这个 webhooks 是在相应的项目中的 settings 中获取的。

然后，我们如何判断 jenkins 有权限呢？

所以，我们需要一个个人的身份验证，这个身份验证是在账号的 settings 获取的，叫做

- Personal access tokens

<br/>

# 整个流程

<br/>

## 新建项目

首先我们要在 github 上，建立一个新的仓库，在这里，我给出的代码是创建一个文件，是以 python 作为编程语言的。

我创建了一个 write 的项目，其包含

- t.py

内容为

```python
def text_create(name, msg):
    desktop_path = "./"  # 新创建的txt文件的存放路径
    full_path = desktop_path + name + '.txt'  # 也可以创建一个.doc的word文档
    file = open(full_path, 'w')
    file.write(msg)  # msg也就是下面的Hello world!
    file.close()


text_create('mytxtfile', 'Hello world!')
```

其地址为

- git@github.com:benpaodewoniu/write.git
- https://github.com/benpaodewoniu/write.git

## 申请身份验证——Personal access tokens

进入 github ，进入自己「账号」的 Settings.

在左侧一栏中有一个 「Developer settings」 ，点击进去后，再点击左侧的 「Personal access tokens」

我们将在右边看到 「Generate new token」。

{% img /images/jenkins/2_1.png %}

上面的信息填写完成就保存。

这个 token 只出现一次，所以，可以复制一份，防止丢失（可申请多个 token，也可以删除没用的 token）。

## 给项目添加 webhooks

webhooks 这个是当代码上传到 github 上，github 通知 jenkins 可以自动的拉取代码，部署。

进入 github 的相关工程，然后在工程主页上，点击 「settings」，在左侧有一个 「webhooks」 ，点击后，再点记右边的 「add webhooks」。

{% img /images/jenkins/2_0.png %}

在「Payload URL」位置填入webhook地址，再点击底部的「Add webhook」，这样就完成webhook配置了，今后当前工程有代码提交，GitHub就会向此webhook地址发请求，通知Jenkins构建。

这个讲一下 Payload URL 应该怎么写。

假设，我们访问机器的 jenkins 的网址为

- http://47.**.1*5.*1*:8090/

那么， Payload URL 应该是

- http://47.**.1*5.*1*:8090/github-webhook

## Jenkins配置

配置GitHub，如下图：

{% img /images/jenkins/2_2.png %}

在打开的页面中，向下拉，拉到 「github」

{% img /images/jenkins/2_3.png %}

填写完 1 之后，再 点击 2 ，出现

{% img /images/jenkins/2_4.png %}

在 「secret」中填写 「Personal access tokens」。

然后在 3 出选择你刚才创建的链接。

然后点击链接测试，如果出现下面就说明没问题。

{% img /images/jenkins/2_5.png %}

然后，点击页面最底部的「保存」按钮。

## 构建项目

{% img /images/jenkins/2_6.png %}

<br/>

{% img /images/jenkins/2_7.png %}

接下来设置 jenkins 的配置信息，分为"源码管理设置"和"构建环境设置"两部分；

### 源码管理设置

{% img /images/jenkins/2_8.png %}

这里面的「Repository URL」要填写成项目地址，如下（要使用https）

- https://github.com/benpaodewoniu/write.git

这里面的「URL」填写的是项目的主页

- https://github.com/benpaodewoniu/write

「Credentials」创建一个Credentials，Kind选择「Username with password」，Username输入GitHub账号，Password输入GitHub密码；

### 构建触发器、构建环境设置、绑定

{% img /images/jenkins/2_9.png %}

在 bindings 我们选择上面配置的 GitHub Server （看上面的 「Jenkins配置」）

### 编写执行脚本

这个是在 「构建后操作」

{% img /images/jenkins/2_10.png %}

<br/>

{% img /images/jenkins/2_10.png %}

编写完上面之后，就可以保存了。

<br/>

# 执行

<br/>

{% img /images/jenkins/2_12.png %}

<br/>

{% img /images/jenkins/2_11.png %}

手动运行的话，可以点击右边绿色的键。

<br/>

# 执行完之后如何查看日志和执行的结果

<br/>

{% img /images/jenkins/2_13.png %}

这里面我们可以根据相应的选项，选择对应的操作，删除、修改等。

而，「workpace」里面就记载了运行结果。

## workspace

{% img /images/jenkins/2_14.png %}

在执行的结果中，我们可以看到我们执行了多少次，如果想要查看执行的结果，可以点击前面的「小球」。

{% img /images/jenkins/2_15.png %}

<br/>

# 后记

<br/>

以后我们提交代码之后，上面配置的是 master 分支，只要我们向 github 相应项目的 master 提交代码，那么， jenkins 就会自动构建、部署、执行，非常方便。

<br/>

# 插件

<br/>

我们在前面写脚本的时候，选择 「Send build artifacts over SSH」

但是，很多人都没有，这是因为他们没有安装一个插件，叫做「Publish Over SSH」

[参考资料](https://tendcode.com/article/Publish-Over-SSH/)

## 添加系统配置

安装完插件之后，需要到 Jenkins 「系统配置」中添加 Publish Over SSH 的配置项，其实就是添加一些远程节点的登陆信息，后续可以用到任务中。

配置项有点类似添加凭证：

{% img /images/jenkins/2_17.png %}

## 构筑后脚本编写

构建完成之后，在 Jenkins 的构建后操作的步骤中找到“Send build artifacts over SSH”的操作步骤，这个就是 Publish Over SSH 的操作动作。

下面是我添加的操作：

{% img /images/jenkins/2_18.png %}


