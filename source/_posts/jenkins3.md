---
title: jenkins | 自定义参数传递
date: 2020-03-10 17:28:35
categories:
- jenkins
tags:
- jenkins
---
我们公司因为要部署不同的币种，所以，进行了不同的币种构造。

- btc
- eth

等，因为，后期币种信息可能会非常的多，所以，自定义参数部署，是现在的当务之急。

<!-- more -->

<br/>

# 参考资料

<br/>

[使用插件实现Jenkins参数化构建](https://www.cnblogs.com/rslai/p/8776684.html)
[Jenkins 参数化构建初探](https://wangtingwei.info/?p=1021)

<br/>

# 安装过程

<br/>

## 安装插件

- Build With Parameters 输入框式的参数
- Persistent Parameter  下拉框式的参数

## git参数化

![](/images/jenkins/3_0.png)

## 使用相关的参数

![](/images/jenkins/3_1.png)

## 在构建的时候选择

![](/images/jenkins/3_2.png)

<br/>

赋予参数值

![](/images/jenkins/3_3.png)