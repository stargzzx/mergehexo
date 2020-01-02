---
title: python scrapy 框架
date: 2018-06-13 11:42:31
categories:
- python
tags:
- 爬虫
- scrapy
- python
- tutorial
- frame
---
这里是 scrapy 框架的讲解。
<!-- more -->
## 介绍
## 安装
{% codeblock %}

创建相关的虚拟环境后，用豆瓣源

	pip install -i https://pypi.douban.com/simple/ scrapy

	但是在安装过程中twisted可能会出错

	所以要到官方网站

		https://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted

	下载适合自己的版本的twisted.whl

	安装完之后，放在任意一个路径即可

	进入虚拟环境，执行

		pip install D:\Twisted-17.9.0-cp36-cp36m-win_amd64.whl

	自主安装twisted

	在执行pip install -i https://pypi.douban.com/simple/ scrapy

	成功后 输入scrapy -h 检验是否安装成功

其实scrapy是一个框架，但不是引进去，需要用下载的scrapy单独创建一个scrapy项目，后续代码需要在这个项目中写

选取一个路径，将项目创建到这个路径下

	首先在正常模式下，进入要创建的项目路径内

	启动相关虚拟机

	输入命令 scrapy startproject Article

	项目创建成功

scrapy项目下的文件夹用途

	spiders文件夹里面是存放具体的某个爬虫

	但是里面的文件是空的，所以可以创建一个要爬的域名项目

	进入到创建好的项目下面 cd... （否则后期创建文件会放在其他目录下面）

	输入命令scrapy genspider 项目名（域名） 域名路径

	会创建一个项目名.py文件

创建完文件后，需要配置虚拟环境的解释器，否则会用默认的全局解释器

	具体如下 

		file-setting-输入interp-找到project interpreter -在右框中选择虚拟环境中的python.exe文件，在相关虚拟环境下的scrapys目录下

以上配置完成，可以直接写代码了

启动scrapy,爬取

	输入命令 scrapy crawl 项目名（都要在虚拟环境的相关目录下进行）

		这个项目名也就是爬虫名

			即为spiders文件夹下，你所创建的一个爬虫类中，name属性的值

		win可能会缺少win32api文件，只需要在pip install pypiwin32在豆瓣源下下载即可

	当然启动项目这条命令可以配置到相关文件中，省去输入这条步骤


from scrapy.http import Request

	用于请求参数

{% endcodeblock %}
## 流程
{% img /images/python/15.jpg %}
{% codeblock %}

调用

	scrapy crawl 爬虫名

从互联网中爬取数据，数据中有response

然后爬虫类中 parse 方法，分析数据

	再将其用 item 容器保存

再做后续处理，比如存储等等

{% endcodeblock %}
## 选择器
{% codeblock %}

selector 是一个选择器，有四个基本方法

xpath():传入一个xpath表达式，返回该表达式所对应的节点列表

css()：传入css表达式，返回该表达式所对应的节点列表

extract():序列化该节点为unicode字符串并返回list

re():根据传入的正则表达式对数据进行提取，并返回unicode字符串列表

{% endcodeblock %}
## XPath 
{% codeblock %}

response.xpath()

排序从1开始

一般直接用F12探查的路径都是有误差的，如果是具体路径最好用查看源代码

但一般浏览器都有提供xpath的方法

/ 是选取子元素

// 选取后代元素

article 选取所有article元素的所有子节点

/article 选取根元素article

article/a 选取所有属于article的子元素的a元素

//div 选取所有div子元素（不论出现在任何地方）

article//div 选取所有属于article元素的后代div元素

//@class 选取所有名为class的属性

/article/div[1] 选取属于article子元素的第一个div元素

/article/div[last()] 选取属于article子元素的最后一个div元素

/article/div[last()-1]

//div[@lang] 选取所有拥有lang属性的div

//div[@lang='eng']

/div/* 选取属于div元素的所有子节点

//* 所有元素

//div[@*] 选取所有带属性的title元素

/div/a|//div/p 选取所有div元素的a和p元素

//span|//ul 选取文档中的span和div

article/div/p|//span 选取所有属于article的div的p元素和所有的span

如果只想获取内容，而不想要标签只需要在后面加入/text()

prise = "//*[@id='113180votetotal']/text()"

prise1 = response.xpath(prise).extract()[0]

提取出的内容str要想获取关键内容

只需要调用extract()方法,事实上在没调用这个方法前，得出的结构还可以用xpath继续调用

	str.extract()

	str = "//*[@id='post-113180']/div[1]/h1/text()"

        h1 = response.xpath(str).extract()

如果某一个元素的一个属性含有多个名字，所以直接调用某一个名字可能出错

可以使用如下命令

	//span[contains(@class,'vote')]

	<div class='a b'>test</div>

		xpath('//div[contains(@class,"a") and contains(@class,"b")]') #它会取class同时有a和b

获取某一属性
	
	 post_url = response.xpath("//div[@id = 'archive']//div[@class = 'post-meta']//p//a[@class = 'archive-title']/@href")
	
	上面的不需要在里面加text()函数	

{% endcodeblock %}
## 编写
{% codeblock %}

建立项目后

定义 Item 容器

	用于保存爬取到的数据的容器，其使用方法和python的字典类似，并提供了额外保护机制来避免拼写错误导致的未定义字段错误
	
	建模

编写爬虫

	编写爬虫类 Spider，在spiders文件夹下的你创建的文件中。

	Spider是用户编写用于爬网站的类

	其中包含一个用于下载的初始URL，然后是如何跟进网页中的链接以及如何分析页面中的内容，还有提取生成item的方法	

存储内容


{% endcodeblock %}