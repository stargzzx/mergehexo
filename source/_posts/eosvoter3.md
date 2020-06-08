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

在 1.0 时期，全部都是手工开启。

一共有两个脚本。

- start_app.sh
- start.sh
- stop.sh

### start_app.sh

主要是开启后台服务

```python
#!/usr/bin/env bash
PROJECT_PATH=/root/eosvoter
ps -ef | grep "${PROJECT_PATH}/main.py" | grep -v "grep" | awk -F " " '{print $2}' | xargs kill -9
ps -ef | grep "${PROJECT_PATH}/pool_data.py" | grep -v "grep" | awk -F " " '{print $2}' | xargs kill -9
nohup python3.7 ${PROJECT_PATH}/main.py > ${PROJECT_PATH}/logs/eos_vote_main.log 2>&1 &
```

### start.sh

主要是开启抓取数据的脚本。

```python
#!/usr/bin/env bash
PROJECT_PATH=/root/eosvoter
nohup python3.7 ${PROJECT_PATH}/eos_vote_calc.py > ${PROJECT_PATH}/logs/eos_vote_calc.log 2>&1 &
nohup python3.7 ${PROJECT_PATH}/eos_voter_app.py > ${PROJECT_PATH}/logs/eos_voter_app.log 2>&1 &
```

一般都是，将代码在服务器拉取最新的代码，然后运行 `start_app.sh` 脚本。

然后运行 `stop.sh` 脚本，来停掉 docker。

### stop.sh

```python
#!/usr/bin/env bash
ps -ef | grep eos_voter | grep -v "color" | awk -F " " '{print $2}'
```

然后自行开启 `docker` ，进入 `docker` 后，拉去最新的代码，然后启用 `start.sh` 脚本。

比较麻烦。

## V1.1

这个版本，用的是 `jenkins` 来自动化开启的。

但是，目前，还没有把服务全部集中在 `docker` 里面，所以部分抓取数据的脚本是集中在物理机上的。

其中，脚本做了如下的改变。

### start_app.sh

```shell script
#!/usr/bin/env bash
PROJECT_PATH=/root/eosvoter
ps -ef | grep "${PROJECT_PATH}/main.py" | grep -v "grep" | awk -F " " '{print $2}' | xargs kill -9
ps -ef | grep "${PROJECT_PATH}/pool_data.py" | grep -v "grep" | awk -F " " '{print $2}' | xargs kill -9
nohup python3.7 ${PROJECT_PATH}/main.py > ${PROJECT_PATH}/logs/eos_vote_main.log 2>&1 &
nohup python3.7 ${PROJECT_PATH}/pool_data.py > ${PROJECT_PATH}/logs/pool_data.log 2>&1 &
```

### start.sh

```shell script
#!/usr/bin/env bash
PROJECT_PATH=/root/eosvoter
nohup python3.7 ${PROJECT_PATH}/eos_vote_calc.py > ${PROJECT_PATH}/logs/eos_vote_calc.log 2>&1 &
nohup python3.7 ${PROJECT_PATH}/eos_voter_app.py > ${PROJECT_PATH}/logs/eos_voter_app.log 2>&1 &
nohup python3.7 ${PROJECT_PATH}/transaction.py > ${PROJECT_PATH}/logs/transaction.log 2>&1 &
```

然后 `jenkins` 的 `Send build artifacts over SSH` 模块下的 `	Exec command` 里面的脚本代码是这样的

```shell script
cd /root/eosvoter
./start_app.sh
docker stop eosvoter
sleep 5
docker rm eosvoter

docker run -dit --net=host --name eosvoter  eosvoter:v2

docker cp /root/eosvoter   eosvoter:/root/

sleep 5

docker exec -i eosvoter /bin/bash -c 'source /etc/profile && cd /root/proxy_pool/cli && sh ./start.sh && cd /root/eosvoter && sh ./start.sh'
```

其整个运行流程是

服务器拉取最新的代码，然后，直接在 `jenkins` 上构建就好了。

![](/images/eos_voter/3_0.png)
