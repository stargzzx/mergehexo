---
title: hexo | 原理解析
date: 2020-02-06 00:21:18
categories:
- hexo
tags:
- hexo
---
这个将会系统的介绍一下 hexo 的原理。

<!-- more -->

<br/>
# 目录以及文件
<br/>

在hexo的主目录下有一下文件和文件夹：

```
	├── _config.yml	    #站点配置文件
	├── db.json            #缓存文件
	├── debug.log       #hexo s --debug 产生的日志文件
	├── node_modules    #nodejs 本地包
	├── package.json    #nodejs 本地配置信息
	├── public               #生成的静态文件所在的文件夹
	├── scaffolds          #新生成page的模板
	├── source             #文章所在文件夹
	└── themes           #主题所在文件夹
```

一个主题的大致结构如下

```
├── _config.yml   # 主题配置文件
├── languages     #语言文件夹
├── layout            # 布局文件夹。用于存放主题的模板文件，决定了网站内容的呈现方式
├── scripts        # 脚本文件夹
└── source       # 资源文件夹，除了模板以外的 Asset，例如 CSS、JavaScript 文件等，都应该放在这个文件夹中。
```