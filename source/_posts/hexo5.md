---
title: MarkdownPad2 编辑器导入 mathJax
date: 2019-01-07 17:10:11
categories:
- hexo
tags:
- MarkdownPad2
- mathJax
- hexo
---
如题，这个还是很重要的。

资料来源如下。

[MarkdownPad2 实现数学公式的live preview 与转pdf操作](http://www.cnblogs.com/sevenir-code/p/5654180.html)

<!-- more -->

在使用MarkdownPad2时，需要插入数学公式，但live preview失效，记录一下修改过程：

在MarkdownPad中，点击"Tools > Options > Advanced > HTML Head Editor"，这个是自定义头文件。添加下列内容：

	<script type="text/javascript"  
	　　src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
	</script>
	
或者可以下载MathJax包到本地，解压到硬盘，同理添加：

	<script type="text/javascript" 
		src="D:/softwares/MarkdownPad 2/jaxedit-0.30/jaxedit-0.30/library/mathjax/unpacked/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
	</script>
	
[MathJax下载地址](https://github.com/zohooo/jaxedit/releases)

为什么要特别强调下载地址呢，因为我们可以看到上面添加的链接没有普遍性，我们下载的 mathJax 版本不一样，所以我们要自己进行地址改编。

可以按F6 preview, （F4改变预览位置，F5是否显示预览， F6浏览器预览， F11全屏）

但是，很不幸的是，我们再实时的可视化右边窗口中，不能看到数学公式。

所以，我们要通过浏览器看， markdown 可以很方便的使用浏览器查看。

额，最好的办法还是经过 hexo s 来查看。






























