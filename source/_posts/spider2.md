---
title: 抓包bilibili APP
date: 2019-06-20 10:12:43
categories:
- spider
tags:
- spider
- 爬虫
- 手机爬虫
- fillder
---
这里是我用爬虫，爬取哔哩哔哩手机APP的全过程。
<!--more-->
# 参考文献
[项目实战 - 使用Fiddler抓取bilibili安卓客户端口数据并分析](https://www.jianshu.com/p/fd4498b32066)
[Bilibili直播弹幕抓取](https://blog.csdn.net/xuchen16/article/details/81064254)
[Bilibili视频爬虫](https://blog.csdn.net/Mike_Shine/article/details/81004136)
[使用Chrome或Fiddler抓取WebSocket包](https://www.cnblogs.com/EasonJim/p/5542719.html)
[python爬虫—爬取b站APP视频信息](https://cloud.tencent.com/developer/article/1422143)
[Fiddler抓部分app时网络连接失败](https://blog.csdn.net/okm6666/article/details/83862997)
[Win10用fildder抓包Android应用，解决手机设置代理后无法上网](https://www.cnblogs.com/wasayezi/p/10131994.html)
[fiddler抓包——手机添加代理后APP连不上网的常见4种原因及解决办法](https://blog.csdn.net/u012106306/article/details/89848765)
[Fiddler抓包工具手机添加代理后连不上网解决办法](https://blog.csdn.net/shuiziliu1025/article/details/80911811)
[fiddler 图标解释](https://blog.csdn.net/wangjun5159/article/details/54024413)
# 项目需求
因为想赚钱，所以，接了一个私活，其实也不是私活，只是项目方让我写一个demo，额，不是爬B站，但是，最后选用了用B站练练手。
# fildder简介
现在的移动应用程序几乎都会和网络打交道，所以在分析一个 app 的时候，如果可以抓取出其发出的数据包，将对分析程序的流程和逻辑有极大的帮助。对于HTTP包来说，已经有很多种分析的方法了，但是现在越来越多的应用已经使用HTTPS协议来和服务器端交换数据了，这无疑给抓包分析增加了难度。可是 Windows系统下的 Fiddler 可以用来解决这个问题。
## 图标解释
使用fiddler抓包时，会看到左侧按照顺序显示session，并且前边有个图标，但通常，不知道图标是什么意思。 
{% img /images/spider/2_0.png %}
参考官方文档
[官方文档](https://docs.telerik.com/fiddler/KnowledgeBase/UIGuide)
{% img /images/spider/2_1.png %}
这里尤其注意“锁图标”这里写图片描述，它用来标记，在网络环境受限时，为https建立http tunnel。
## fildder配置
原来我是打算用手机来做中间的媒介，不过，后来发现手机有点问题，所以，我最后用的是雷电模拟器。
下载好fildder后，需要配置它。
选择Tools菜单
{% img /images/spider/2_2.png %}
切换到Connections标签，填写要监听的端口（如：8888），将下方3个钩勾上，最后点击OK关闭设置界面。
{% img /images/spider/2_3.png %}
## 模拟器设置
获取电脑的IP地址。
{% img /images/spider/2_4.png %}
在雷电中打开手机模拟器，找到 WLAN。
{% img /images/spider/2_5.png %}
{% img /images/spider/2_6.png %}
鼠标左键长按，在弹出的对话框中，按照下面的步骤填写相关信息。
{% img /images/spider/2_7.png %}
{% img /images/spider/2_8.png %}
## 开始抓包
>经过上面的步骤，就可以抓包了

此时如果在Fiddler中有太多请求记录，不方便我们查看接下来要抓的数据，可以进行如下操作将这些记录清除。
{% img /images/spider/2_9.png %}
当我对雷电模拟器的哔哩哔哩APP进行操作后，fildder就已经可以抓取数据了。
{% img /images/spider/2_10.png %}
但是，有点奇怪的是，我们抓取的包都带小锁，这是因为以上的配置都是在抓取http的信息，而不是https的信息。
在Fiddler中没找不到带有Json图标的请求记录，但有2个带锁的请求，而且Host显示"Tunnel To"，这就说明APP采用的是https请求
## https配置
{% img /images/spider/2_11.png %}
在选择上述的选项中会弹出一系列弹窗，只需要全部选择 YSE 就好了。
这个fildder的客户端配置好了，所以，接下来我们还要给模拟器安装上证书。
## 安装证书
Fiddler可以抓取支持http代理的任意程序的数据包，如果要抓取https会话，要先安装证书。
用手机浏览器访问，上面的IP：端口号。
{% img /images/spider/2_12.png %}
{% img /images/spider/2_13.png %}
证书的名字随便就可以，这样就可以抓取手机APP的https信息了。
>可能在安装证书的时候会要求你为手机设置锁屏密码，随便设置一个你能记住的密码就好了，如Pin码：1234。

## 再次抓包
{% img /images/spider/2_14.png %}
这个时候，我们就可以看到信息已经成功的抓取了。

# 可能存在的坑
## 设置代理后部分APP抓取不到
这个有点奇怪，第一是，我在手机上尝试连接代理的时候，浏览器可以上网，部分APP可以上网，但是有的就不能上网，比如，哔哩哔哩就不能上网，但是网易漫画就可以。
所以，我查了一下网上的教程，进行修改，大概如一下的步骤：
### 修改注册表
{% img /images/spider/2_15.png %}
在HKEY_CURRENT_USER\Software\Microsoft\Fiddler2下创建一个DWORD，值置为80（十进制） 
{% img /images/spider/2_16.png %}
编写fiddlerScript rule，

在fiddler菜单栏，点击Rules->Customize Rules，用Ctrl+F查找OnBeforeRequest方法添加一行代码
{% codeblock %}

if (oSession.host.toLowerCase() == "webserver:8888") 
{
        oSession.host = "webserver:80";

}

{% endcodeblock %}
{% img /images/spider/2_17.png %}
{% img /images/spider/2_18.png %}
不过令人奇怪的是，我的手机设置完这些后，还是部分APP不能上网，我觉得是证书安装的方式不对，所以，我访问一下 https 的网址，结果，是可以访问的，额，这就奇怪了。
另外，我在使用模拟器的时候，将上面的注册表还原，也就是没加那些东西，但是，可以获取哔哩哔哩和淘宝直播的数据。
额，所以，暂时只能归结于设备问题了。
# 技巧
## 使用HiJson代替Fiddler自带的json查看窗口
很多时候，我都会使用HiJson来帮助我完成对接口返回数据的分析，我相信大多数安卓开发者对该工具应该不会陌生。不过，HiJson不支持直接数据请求，所以需要从别处将json数据复制到HiJson中，Fiddler的WebView窗口可以帮到我们。
{% img /images/spider/2_19.png %}
{% img /images/spider/2_20.png %}
将WebView窗口中的数据全选，右键，复制。打开HiJson，粘贴到左窗口后点击“格式化JSON字符串”。
{% img /images/spider/2_21.png %}
## 过滤器
如果没有设置过滤规则，就会抓取所有的信息，太影响体验。
{% img /images/spider/2_22.png %}



