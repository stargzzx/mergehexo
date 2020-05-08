---
title: spider | 回归朴素
date: 2020-02-23 22:41:37
categories:
- 爬虫
- 电脑
tags:
- spider
---
这次接到任务，让我整理百家姓，真是，让人头痛，所以，第一时间想到的就是爬虫爬数据，于是果断的想到了之前的爬虫架构 scrapy。

但是，最后证明使用 scrapy 很笨拙。

<!-- more -->

首先展示一下，我要爬取的网站。

[百家姓](https://so.gushiwen.org/guwen/book_16.aspx)

为什么那个显得笨拙呢，主要是，这个网站的所有文字使用的是无标签的 <p></p>

而，scrapy 最好是，模块化，每一个结构有自己的分类，比如代码像是

	<h1>李</h1>
	<h2>历史来源</h2>
	<p class="origin">。。。。</p>

像是这种分类明确的标签

而，这个网站的展示，如下面这样

![](/images/spider/7_0.png)

这样对我造成了极大的分类难度，但是，如果我们得到这个网页，也可以很好的分类出来，但是，scrapy 很笨重，分类就不是很好分（主要是当时对 scrapy 理解就是解读 模块化 代码，其实，现在想想，scrapy 一样可以分析这种网页）

所以，我的目标就变成了，如何应付老师，我自己尝试了一下，花了 1 个小时，搜集了 40 多个百家姓，真的效率低，所以，干脆不收集了。

但是，昨天老师突然问我要初稿，我很懵逼，所以，我只能使用了搬砖技巧，ctrl + c/v

但是，一会，我就烦了，所以，使用了朴素爬虫

	requests + beautifulsoup

结果，十分钟就完成了任务。

下面把代码贴一下

```python
import requests
from bs4 import BeautifulSoup
from collections import defaultdict
import docx
import re

urls = ['这里本来是每一个姓的url，一共是 438 个，但是篇幅太长了，我就不放了，你们可以根据代码去整理，很简单']

name_info = defaultdict(list)

for u in urls:
    response = requests.get(u).text
    response = response.replace('<br />', '</p><p>')
    soup = BeautifulSoup(response, "html.parser", from_encoding="utf-8")
    name = soup.find_all("h1")[0].text.strip()
    print(name)
    origin_come = []
    famous_person = []
    live_area = []
    save = None
    p_list = soup.find_all("p")
    for p in p_list:
        t = p.text.strip()
        if t == '历史来源':
            save = origin_come
            continue
        elif t == '家族名人':
            save = famous_person
            continue
        elif t == '地望分布' or t == '迁徙分布':
            save = live_area
            continue
        if save is not None and t != '':
            save.append(p.text.strip())
    tmp = {
        'origin_come': origin_come,
        'famous_person': famous_person,
        'live_area': live_area,
    }
    name_info[name].append(tmp)

doc = docx.Document()
for name, info in name_info.items():
    doc.add_heading(name, 0)
    data = info[0]
    doc.add_heading('来源出处', 1)
    origin_come = data['origin_come']
    for i in range(len(origin_come)):
        doc.add_paragraph(origin_come[i])

    doc.add_heading('历史名人', 1)
    famous_person = data['famous_person']
    for i in range(len(famous_person)):
        doc.add_paragraph(famous_person[i])

    doc.add_heading('地望分布', 1)
    live_area = data['live_area']
    for i in range(len(live_area)):
        doc.add_paragraph(live_area[i])

doc.save('./test.docx')
```

这里面有几个地方需要解释一下：

# 规范网页结构

在爬取的过程中，我发现有的网页代码挺混乱的，比如下面：

正常的：

![](/images/spider/7_1.png)

不正常的：

![](/images/spider/7_2.png)

王 字的 历史名人 就没在 p 标签内，所以，我们原来的代码运行就解析不出来。

因为，解析不出单个的 历史名人 ，而是 *****，历史名人（历史名人在一句话中）。

![](/images/spider/7_3.png)

这种是内容不在 p 中，但是，仔细观察，前面都有一个 

	<br>

所以，我们才有了下面的代码

	response = response.replace('<br />', '</p><p>')

让所有的内容都放在 p 标签内。

# 一个编程技巧

我在想，一个姓需要搜集三个信息，那么，怎么才能合理的添加到不同的数组中，后来，我使用了指针


	for p in p_list:
	        t = p.text.strip()
	        if t == '历史来源':
	            save = origin_come
	            continue
	        elif t == '家族名人':
	            save = famous_person
	            continue
	        elif t == '地望分布' or t == '迁徙分布':
	            save = live_area
	            continue
	        if save is not None and t != '':
	            save.append(p.text.strip())

写完这些代码后，我感觉自己的代码水平又进步了。

我在里面还用了 python-word 这个库，可以顺便生成 docx ，真香，下面看一下效果：

![](/images/spider/7_4.png)

这里特别吐槽一下，这里的百家姓一共搜集了快 37 万字了，老师，还想叫我一星期给他们出一本书，真的无语，也不看看这是什么工作量，真的自私。。。

这里，真的对那些坑蒙拐骗的老师感到羞耻，我他妈一个计算机系的人竟然出教材，还是一星期最少 66 页的书，为了应付真的是不择手段。。。
