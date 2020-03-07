---
title: 动态背景
date: 2019-10-07 13:46:41
categories:
- hexo
tags:
- hexo
- 动态背景
---
这个挺简单的，当然，也会有一点点小坑。

<!--more-->

<br/>

# 参考资料

<br/>

[next主题如何添加动态背景](https://blog.csdn.net/qq_33699981/article/details/60330891)

<br/>

# 文章内容

<br/>

## 修改_layout.swig

打开 next/layout/_layout.swig 

在 < /body>之前添加代码(注意不要放在< /head>的后面)

	{% if theme.canvas_nest %}
	<script type="text/javascript" src="//cdn.bootcss.com/canvas-nest.js/1.0.0/canvas-nest.min.js"></script>
	{% endif %}

## 修改配置文件

打开 /next/_config.yml,在里面添加如下代码：

	canvas_nest: true
	
但是，有的配置文件中有这个属性，所以不需要你再写一遍，另外，在某一个属性下面也有一个类似的 canvas_nest 但是，那个不行。

{% img /images/hexo/8_0.png %}

图片中的那个不是，这个要切记。