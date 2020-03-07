---
title: hexo | 再弄搜索框
date: 2020-03-07 10:12:09
categories:
- hexo
tags:
- hexo
---
由于，我的线上标签问题没有解决。

[hexo | 标签相关的工作](https://benpaodewoniu.github.io/2020/03/07/hexo16/)

导致我在线上用标签查找数据的时候，查找不到。

这个时候，我想起了运用搜索框，曾经，我弄过搜索框。

[博客添加搜索框](https://benpaodewoniu.github.io/2018/11/08/hexo1/)

但是，当时由于出了问题，就放弃了，现在再弄的时候，发现了当年的步骤确实有问题。

<!-- more -->

<br/>

# 当年错误的分析

<br/>

- next 5.0 之后的版本自带搜索框，不需要自己添加代码
- 生成的 search.xml 很小，之前，貌似是眼花，看成了 600+ M 数据。

<br/>

# npm 出现错误

<br/>

我在安装插件的时候， npm 出现错误。

关于这个的解决，请看我下面的博文

[npm | run 'npm audit fix' to fix them, or 'npm audit' for details](https://benpaodewoniu.github.io/2020/03/07/npm1/)

<br/>

# 步骤

<br/>

[Hexo Next 主题中添加本地搜索功能](https://blog.csdn.net/lijing742180/article/details/87970909)

## 主题配置文件

在主题配置文件 _config.yml 中找到如下内容：


	local_search:
	  enable: true
	  trigger: auto
	  top_n_per_article: 1

确保 enable 设成 true。

top_n_per_article 字段表示在每篇文章中显示的搜索结果数量，设成 -1 会显示每篇文章的所有搜索结果数量。

这个时候，页面就已经出现搜索框了，但是，现在还不能用，必须安装插件。

## 安装插件

	# 安装插件，用于生成博客索引数据（在博客根目录下执行下列命令）：
	npm install hexo-generator-search --save

安装之后，会在站点目录的 public 文件夹下创建一个 search.xml 文件。

## 修改站点配置文件

在站点配置文件 _config.yml 中添加如下内容：


	# Search 
	search:
	  path: ./public/search.xml
	  field: post
	  format: html
	  limit: 10000

- path：索引文件的路径，相对于站点根目录
- field：搜索范围，默认是 post，还可以选择 page、all，设置成 all 表示搜索所有页面
- limit：限制搜索的条目数

这个时候就能使用搜索了。

讲真的，搜索真香～～～

到后面，使用

	hexo g

其，就自动生成 search.xml 文件了。