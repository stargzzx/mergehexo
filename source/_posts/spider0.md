---
title: scrapy的选择器
date: 2019-03-05 09:22:37
categories:
- spider
tags:
- python 第三方库
- spider
- 爬虫
- python
- 选择器
- css 选择器
- xpath 选择器
---
这篇主要是爬虫的选择器。
<!--more-->
## 选择器分类
selector 是一个选择器，有四个基本方法
xpath():传入一个xpath表达式，返回该表达式所对应的节点列表
css()：传入css表达式，返回该表达式所对应的节点列表
extract():序列化该节点为unicode字符串并返回list
re():根据传入的正则表达式对数据进行提取，并返回unicode字符串列表
## css选择器

	* 选择所有节点

#container 选择id为container的节点
.container 选取所有class包含container的节点
li a 选取所有li下的所有a节点
ul + p 选取ul后面的第一个p元素
div#container > ul 选取id为container的div的第一个ul子元素
ul ~p 选取与ul相邻的所有p元素
a[title] 选取所有有title属性的a元素
a[href = "111"] 选取所有href为111的元素
a[href^="http"] 选取所有a的href属性以http开头的所有元素
a[href$="com"]
input[type = radio]:checked 选择选中的radio的元素（按钮）
div:not(#container)
li:nth-child(3) 选取第三个li元素
li:nth-child(2n) 偶数li
用法和xpath差不多，也是借助response
response.css()
选取关键内容也是text不过是

	response.css(".container h1::text").extract()

## XPath
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


