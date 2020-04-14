---
title: software | postman
date: 2019-10-05 20:47:24
categories:
- software
- 电脑软件
tags:
- software
- postman
---
这是每一个开发者都必备的神器。

<!--more-->

关于这个的下载和安装在这里不说了，自己搜相关的内容吧，在这里我只是提一下提高效率的方法。

<br/>

# 项目的路径规整

<br/>

我们会在 collections 中，创建我们的项目URL合集。

![](/images/software/1_0.png)

然后我们可以根据不同的URL的范围，在 collections 中，我们可以创建不同的文件进行存储。

![](/images/software/1_1.png)

然后我们在相应的文件夹中，放置相应的请求。

<br/>

# get 和 post 请求

<br/>

## get

get的请求是在 params 中添加参数。

![](/images/software/1_2.png)

## post

post的请求是在 body 中的 form-data 填写数据。

![](/images/software/1_3.png)

<br/>

# 占位符

<br/>

我们经常会在本地和线上做测试，所以，要填写不同的地址，这个时候，我们就可以用占位符来减少我们的工作量。

## 添加
![](/images/software/1_4.png)

![](/images/software/1_5.png)

我们创建完成相应的环境之后，我们就可以填写占位符，比如，下面我们填写了 host 占位符。

![](/images/software/1_6.png)

后面的两个地址，第一个是初始地址，第二个是正在使用地址，通常，我们将线上地址写在初始地址，然后正在使用地址根据情况来填写。

然后，为了使用这个环境，我们要选择一下这个环境。

![](/images/software/1_7.png)

然后我们根据具体的需要，来随时更改我们的环境变量。

![](/images/software/1_8.png)

判断是否激活了环境，变量有没有替换，可以将鼠标移到占位符上。

![](/images/software/1_9.png)

我们的占位符是 host ，那么我们就可以在地址的相应位置使用 {{host}}

	http://{{host}}/eosvoter/v1/user/login
	
![](/images/software/1_10.png)

<br/>

# 请求文件下载

<br/>

使用 send and download 就好了。

![](/images/software/1_11.png)

<br/>

# 上传文件

<br/>

![](/images/software/1_12.png)

<br/>

# POST 请求 500 错误

<br/>

有的时候我们使用 postman 会出现 500 的错误，这有可能是我们传值的方式错误

所以，我们也变更我们传值的方式。

之前错误的传值方法

![](/images/software/1_13.png)

另一种表现形式

![](/images/software/1_14.png)

这种可能会导致出现 500 错误，所以，我们要更改传值方式，变成 通过 raw 传值

![](/images/software/1_15.png)

错误的原因：

其实就是json格式的问题

header就不用说了：   

	Content-Type:application/json

![](/images/software/1_16.png)

<br/>

# 返回 code

<br/>

postman 还能直接返回 code 形式

![](/images/software/1_17.png)

![](/images/software/1_18.png)




