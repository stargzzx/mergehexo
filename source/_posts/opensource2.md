---
title: 开源项目 | proxy_pool 在服务器部署
date: 2020-04-28 14:01:25
categories:
- 开源项目
- python
- proxy_pool
- 基础
tags:
- 开源项目
---
我是在 docker 中部署的这个项目，为了确保其稳定运行，我使用了下面的两个措施。

<!-- more -->

## docker 和 宿主机 连接方式为 host

	docker run -itd --net=host proxy_pool:v1

## 修改原来的 start.sh 文件

改为

```sh
#!/usr/bin/env bash
nohup python3.7 proxyPool.py webserver > /root/proxy_pool/log/webserver.log 2>&1 &
nohup python3.7 proxyPool.py schedule >  /root/proxy_pool/log/schedule.log 2>&1 &
```

