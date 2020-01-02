---
title: blog迁移
date: 2018-11-26 21:33:29
categories:
- hexo
tags:
- hexo
---
今日承蒙老师厚爱换了一个电脑，所以，我就将数据迁移到新的电脑上。
过程不复杂，另外，真的是承蒙厚爱。
<!-- more -->
## 前期工作
首先将 git ，Node.js 都事先安装到电脑上。
## 安装 hexo
命令如下

	npm install -g hexo
	
## 建立站点
随意在自己喜欢的地方创建一个目录。我是 E:\site\blog
然后在 cmd 下进入这个目录。
将原来的数据 copy 到这个目录下，当然不需要全部拷贝，只需要将下面五个 copy 过来就好了。

	_config.yml
	package.json
	scaffolds/
	source/
	themes/
	
## 模块安装
依次执行以下命令

	npm install
	npm install hexo-deployer-git --save
	npm install hexo-generator-feed --save
	npm install hexo-generator-sitemap --save

## 部署

	hexo g
	hexo d
	
在部署的过程中会遇到让你输入用户名和密码的选项，只要按要求输入就没有问题。
我们在迁移的过程中会碰到很多坑，当然，我可能比较幸运，自己都解决了，但是还是贴出一个填坑的链接吧。
[Hexo搭建Github-Pages博客填坑教程](https://www.jianshu.com/p/35e197cb1273)
