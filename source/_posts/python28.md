---
title: PIL ImageDraw 模块函数详解
date: 2018-12-03 20:28:09
categories:
- python
tags:
- PIL
- pillow
- python
- tutorial
---
这里是介绍 pillow Image 模块的章节。
OK，enjoy it。
<!-- more -->
# ImageDraw

## ImageDraw.Draw 模块

### arc
PIL.ImageDraw.Draw.arc(xy, start, end, fill=None)
draw.arc((150,150,250,250),0,180,'red')
以(200,200)为圆心，50为半径，作一开口向上的红色半圆弧


### chord
PIL.ImageDraw.Draw.chord(xy,start,end,fill=None,outline=None)
draw.chord((150,150,250,250),180,360,'blue','green')
以(200,200)为圆心，50为半径，作一外边缘绿色，填充蓝色弓形



### ellipse
PIL.ImageDraw.Draw.ellipse(xy, fill=None, outline=None)
draw.ellipse((150,150,250,350),'yellow','green')
在给定范围内作一外边缘绿色，填充黄色的椭圆

### line
PIL.ImageDraw.Draw.line(xy, fill=None, width=0)
	
	draw.line(((50,60),(270,356)),'red',5)

在给定两点间作一宽5像素的红色直线

### pieslice
PIL.ImageDraw.Draw.pieslice(xy,start,end,fill=None,outline=None)
draw.pieslice((150,150,250,350),0,90,'gold','purple')
在给定范围内作一外边缘紫色，填充金色的扇形

### point
PIL.ImageDraw.Draw.point(xy, fill=None)
draw.point((150,150),'black')
将指定点改成黑色


### polygon
PIL.ImageDraw.Draw.polygon(xy, fill=None, outline=None)
draw.polygon(((10,10),(10,20),(20,10)),'gold','brown')
棕色连接给定点，金色填充的多边形

### rectangle
PIL.ImageDraw.Draw.rectangle(xy, fill=None, outline=None)
draw.rectangle((150,150,250,350),'teal')
给定范围作一蓝绿色长方形


### text
PIL.ImageDraw.Draw.text(xy, text, fill=None, font=None, anchor=None, spacing=0, align=”left”)
draw.text((100,100),'Hello Triagen!','green')
以指定位置为左上角用绿色写出指定字符

### textsize
PIL.ImageDraw.Draw.textsize(text, font=None, spacing=0)
返回给定text的像素大小

### setfont
PIL.ImageDraw.Draw.setfont(font)
指定默认字体

## D


### Draw
Draw(image) ⇒ Draw instance
含义：创建一个可以在给定图像上绘图的对象。
（IronPIL）用户可以使用ImageWin模块的HWND或者HDC对象来代替图像。这个允许用户直接在屏幕上绘图。
注意：图像内容将会被修改。
{% codeblock %}

from PIL import Image, ImageDraw
im1 = Image.open('2.png')
draw = ImageDraw.Draw(im1)
draw.line((0,0) +im1.size, fill=128)
im1.show()
	# 给图片添加一条线
	
{% endcodeblock %}