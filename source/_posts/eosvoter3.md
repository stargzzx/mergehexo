---
title: eosvoter | 代码的书写以及部署
date: 2019-12-08 08:27:39
categories:
- [项目经历,中型,eosvoter]
tags:
- eosvoter
- 币币生息
---
为了不让流程忘记，所以，特地来记录一下部署。

<!-- more -->

<br/>

# 前端

<br/>

## V1.0

前端框架是采用 vue 来进行书写的，并且，前后端是完全进行分离的，在本地写前端代码的时候，要先开启后端服务。

因为项目目前具有一定的特殊性，我还没有完全吃透代码，所以，很多步骤我也只是照本宣科的执行一遍。

关于纯 `vue` 如何部署，你可以看我下面的专栏和文章

- [vue 专栏](http://localhost:4000/categories/%E7%BD%91%E7%AB%99%E8%AE%BE%E8%AE%A1/%E5%89%8D%E7%AB%AF%E8%AE%BE%E8%AE%A1/vue/)
- [vue | 在 MacBook 上部署 web 服务](http://localhost:4000/2020/05/27/vue4/) 

### 开发环境

`clone` 完代码后

环境需要全局按照 mgbox

```bash
npm i -g mgbox
```

按照前端依赖

```bash
yarn 
```

启动

```bash
npm run dev
```

### 前端上线部署

1. 执行release.sh脚本

```bash
release.sh
```

这个 `release.sh` 的代码如下

```bash
#!/bin/bash

rm -rf ./dist
rm -rf ./eosvoter.tar.gz

npm run publish

mv ./dist/template/vue/* ./dist/static/

tar zcvf eosvoter.tar.gz ./dist/static/*

scp -P *** ./eosvoter.tar.gz root@***:/var/www
```

2. 进入服务器***:/var/www目录下执行release.sh脚本即可。

```bash
./release.sh
```

这个 `release.sh` 的代码如下

```bash
rm -rf ./html/*
rm -rf ./dist
tar zxvf eosvoter.tar.gz -C ./
mv ./dist/static/* ./html

echo '部署成功！'
```

以上便是前端从运行到部署的全部流程。

<br/>

# 后端

<br/>

## V1.0

在 1.0 时期，