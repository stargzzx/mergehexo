---
title: 实验室传承系统的搭建
date: 2020-09-18 14:41:37
categories:
- [项目经历,中型,labwebs]
tags:
- labwebs
---
应老师要求已经自身的现有的技术，结合开源项目，搭建了一个高分布式、组件式网站的多人合作传承系统。

别看上面这么高端，其实就是一个个网站的集合体，只不过，各个网站之间是独立的。

<!-- more -->

<br/>

# 依附的平台与技术

<br/>

- ubuntu18.04
- github
- gitee
- 百度网盘
- 163 账号
- hexo
- PicGo

<br/>

# 大体思路

<br/>

其实就是做一个主网站，这个主网站用来集合其他的分网站。假设，你注册的 `github` 的名称是 `lab`。

那么，你主网站的仓库名字就应该是

- lab.github.io

懂得都懂吧，上面的名字意味着什么。

然后，在 `github` 上创建其他项目，比如，我想做一个统计各个老师项目的网站，那么，我会在 `lab` 账号下，创建一个名字叫 `project` 的项目，然后使用 `gh-pages` 分支，来将这个网站进行可视化。

所有的可视化，都通过 `hexo` 来完成。

为啥要用到 `gitee` 呢，因为 `github` 有的时候访问实在是太慢了，所以，我建议 `github` 存文字，`gitee` 存图片。

所以，我将从三个方面进行讲述。

- 主网站的建立
- 分网站的建立
- 图床的建立

<br/>

# 开始之前

<br/>

1. 我建议你的生产环境是 `ubuntu` 这样可以省去很多麻烦
2. 我建议你单独申请一个 163 邮箱，然后用这个邮箱去注册上述平台

<br/>

# 主网站的建立

<br/>

ps: 没有权限的加 	`sudo`

再次 ps: 把本地的 `ssh key` 放在 `github` 上。

无论是主网站还是分网站都是 `hexo` 创建的，所以，在这里，我建议你优先看官网。

- [hexo 官网](https://hexo.io/zh-cn/docs/)


ps: 这个官网估计都没人维护，很多信息也都过时了，其实，只要熟悉整个技术，根本不需要按照官网的流程来。官网的太麻烦了！！！

你需要安装 `node`,`git` 这个可以根据百度或者其他官网教程安装。

但是，有一点需要注意的是，你的 `node` 和 `git` 的版本最好是新一点的，这样，不会造成版本冲突问题。

如果，在后续，你是用 `hexo` 的相关命令，诸如

	hexo init

等，出现错误，首先要查看你的 `node` 版本是否能和 `hexo` 的版本对得上。

|Hexo 版本|	最低兼容 Node.js 版本|
|---|---|
|5.0+|	10.13.0|
|4.1 - 4.2|	8.10|
|4.0|	8.6|
|3.3 - 3.9|	6.9|
|3.2 - 3.3|	0.12|
|3.0 - 3.1|	0.10 or iojs|
|0.0.1 - 2.8|	0.10|

## 安装 hexo

其实过程非常简单，根本没有网上其他教程来的那么复杂，按照官网来就行。

全局安装 `hexo` 客户端

	npm install -g hexo-cli

`npm` 相当于是 `python` 的 `pip` 这一类型， `hexo-cli` 就是 `node` 的一个第三方库。

然后进入你的文件夹

	mkdir labwebs
	cd labwebs
	hexo init // 初始化 hexo
	npm install // 安装相关依赖

这个时候其实就创建好了。

当然，你也得安装一下

	npm install hexo-deployer-git --save

这个是 `hexo` 上传给某一地址的插件，如果没有的话，你想把网页传到 `github` 会遇到

	ERROR Deployer not found: git

当然，这也有一个注意的点是，`npm` 一般有两种安装方式，全局安装和当前目录安装，如果是全局安装，那么，你可以在任意一个地方使用，如果只是当前目录安装，那么，你只能在当前目录使用。

比如，上面

- hexo-cli 是全局的
- hexo-deployer-git 是当前的

如果，你想知道更多相关的知识，请看我下面的专栏。

- [npm](https://benpaodewoniu.github.io/categories/%E7%BD%91%E7%AB%99%E8%AE%BE%E8%AE%A1/npm/)

OK，在这里假设你一切顺利，那么，你已经可以通过 `hexo` 的相关命令可以看到成果了。

下面的就是来配置相关的 `_config.yml` 。

这里主要是注意两点

- 下载你喜欢的 theme，并且在 labwebs 中配置它
- 完善上传信息

以这个举例，主要是在 `_config.yml` 中填写

```
deploy:
  type: git
  repo: https://github.com/labwebs/labwebs.github.io
  name: labwebs
  email: 163 邮箱
  branch: master
```

当你上传之后，就会发现这个网站已经可以访问了。

另外，在 `master` 中，我们上传的是 `hexo` 生成的静态网页，但是，如果想要多人编辑数据的话，还需要将原数据添加上去。

在这里，推荐一下我的博文。

- [多个电脑协同更新hexo](https://benpaodewoniu.github.io/2019/10/07/hexo9/)

ps: 其实这个博文，在我现在看来，这个方式也落伍了，如果再叫我选择的话，我就会把静态网页上传到 `gh-pages` 分支上，数据上传到 `master` 分支上，就是我下面分网站要介绍的，这样就不用创建两个仓库了。

<br/>

# 分网站的建立

<br/>

其实，和主网站差不多，但是，在细节上还是有区别的，我还是要走一个差不多的完整流程吧。

这里主要是用到 `github` 的 `gh-pages` 分支的特性。

这里主要是想给实验室的项目做一个网站。

	cd ~
	mkdir project
	cd project
	hexo init
	npm install
	npm install hexo-deployer-git --save

一系列流程之后。

- 选择自己喜欢的 theme
- 修改 `_config.yml`

关于 `_config.yml` 这里面有两个注意的点，一个是

## 更换 url

在 `hexo` 的 `_config.yml`。

```
url: https://labwebs.github.io/project
root: /project
```

如果你不修改，虽然你在本地看的是正确的，但是，上传上去，是获取不到资源的。

因为，原始的 `root` 的内容是 `/`

而你的 `project` 的资源文件路径类似于下面

- https://labwebs.github.io/project/1.html

不修改，你就变成

- https://labwebs.github.io/1.html

会导致 `404`。

## 配置上传

```
deploy:
  type: git
  repo: https://github.com/stargzzx/project.git
  name: labwebs
  email: 163 邮箱
  branch: gh-pages
```

这样你们直接通过 `hexo` 上传的时候，就会把静态页面上传到 `gh-pages` 分支。

## 上传数据

这个时候，我们就可以把当前的数据上传到 `master` 分支了。

首先，创建一个 `.gitignore` 文件，然后添加不需要上传的文件，我的是

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

然后初始化 `git`

	git init
	git remote add origin 「ssh 地址」

然后，就是 `git` 上传的一系列流程。

ps: 这里需要注意的是，有的时候，我们新创建的分支的时候不管用，那么你就搜索一下如何强制创建分支。但是，就目前这个教程来说，好像不存在强制创建分支的时候。

关于 `gh-pages` 的其他特征，以及是否还需要 `github` 做其他的操作。

在我看来，这样就已经可以使用了，但是，保不齐，有的人不行，所以附上我的另一篇博文作为参考。

- [gitbook | 上传到 github](https://benpaodewoniu.github.io/2020/08/11/gitbook1/)

至此，你可以在主网站上附上分网站的链接，这样就可以作为一个个的独立网站的存在。

<br/>

# 图床的建立

<br/>

我们使用 `gitee + PicGo` 原因是，`github` 如果不使用特殊方法的访问太慢了，这样的话，不如把占用资源的东西放到国内。

## 参考文献

