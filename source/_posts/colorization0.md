---
title: 开源项目 | InstColorization
date: 2020-03-15 22:51:27
categories:
- [开源项目,深度学习,InstColorization]
tags:
- InstColorization
---
这个项目，是利用深度学习给黑白图片染色。

最后我在集群上成功部署。

<!-- more -->

<br/>

# 参考资料

<br/>

- [项目地址](https://github.com/ericsujw/InstColorization)
- [论文地址](https://arxiv.org/abs/2005.10825)

<br/>

# 安装项目

<br/>

## 集群背景

- ubuntu16.04
- anaconda3

## clone 代码

	git clone https://github.com/ericsujw/InstColorization
	cd InstColorization

## 安装依赖

值得称赞的是，安装过程可以按照项目中的流程一直执行下去，不存在墙的影响。

	conda env create --file env.yml

## 激活环境

	conda activate instacolorization

## 安装依赖

	sh scripts/install.sh

## 下载 checkpoint

这个是
