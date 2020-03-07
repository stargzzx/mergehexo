---
title: PIL ImageFont 模块函数讲解
date: 2018-12-11 21:53:40
categories:
- python
tags:
- python 第三方库
- python
- PIL
---
如题。

<!-- more -->

<br/>

# T

<br/>

## truetype

PIL.ImageFont.truetype(font=None, size=10, index=0, encoding='')

加载一个TrueType或OpenType字体文件，并创建一个字体对象。此函数从给定的文件加载字体对象，并为给定大小的字体创建字体对象。

参数:

	font：字体–TrueType字体文件。在Windows之下，如果文件在这个文件名中找不到，加载程序也会在Windows字体/目录中查找。
	size：字体大小
	index：索引——哪个字体要加载（默认是第一个可用的面）。
	encoding：编码——使用哪种字体编码（默认是unicode）。常见的编码是“新闻中心”（Unicode），“符号”（微软的标志），“Adob”（Adobe），“项”（Adobe专家），和“Click”（Apple Roman）
	
### 输出中文

{% codeblock %}
# -*- coding: utf-8 -*-
from PIL import ImageDraw,Image,ImageFont
image = Image.new('RGB',(100,100))
draw = ImageDraw.Draw(image)
Font = ImageFont.truetype('C:\Windows\Fonts\simsun.ttc',30,encoding='utf-8')
draw.text((10,10),'程序猿',fill=(255,255,255),font=Font)
image.save('test.jpg')
{% endcodeblock %}
{% img /images/python/35_0.jpg %}