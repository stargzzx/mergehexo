---
title: 树莓派下 pillow 得 show 不能显示
date: 2019-01-14 21:27:54
categories:
- raspberry
tags:
- 树莓派
- pillow
---
因为工程需要，我们需要在树莓派上画图，但是，即便是执行 pillow 没错误，还是没有图像显示出来。

<!--more-->

## 参考资料

[Linux系统下python Image.show()无反应的解决办法](https://blog.csdn.net/wzheng92/article/details/79504654)

[树莓pi不会显示pil图片](https://stackoverrun.com/cn/q/8597972)

## 问题描述

{% codeblock %}
from PIL import Image 
im = Image.open(“xxx.jpg”) 
im.show() 
{% endcodeblock %}

但是实践过程中发现，我的程序虽然能正常运行，但是无法弹出窗口显示图片。并且经过实验还能够顺利将图像保存到本地。

## 原因

虽然树莓派已经有自己的图片显示器了，但是，python 默认的图片浏览器是 ImageMagick 。

所以，我们要安装 ImageMagick 。

image.show（）调用外部查看器程序。如果你没有一个，或者在非图形环境下运行，它不会执行任何操作。

## 解决

在Ubuntu/Debian的情况下：

	sudo apt install imagemagick 
	
之后重启脚本，问题解决。

## 其他注意事项

如果没有显示器，那么我们如果要在其他电脑上连接看图像的话，要用 VNC ，这个我的博客中也有。

如果你从图形化转到命令行中，执行相关代码的话，会出现类似下面的东西。

	<PIL.jpegimageplugin.jpegimagefile image mode=RGB size=1280x720 at 0x1486690>
	

