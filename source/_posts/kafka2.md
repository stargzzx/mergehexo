---
title: kafka | 获取数据
date: 2020-07-15 15:37:42
categories:
- 中间件
- kafka
tags:
- kafka
---
对于 kafka 我还不是很了解，智能暂时记录一下相关的命令。

<!-- more -->

	./kafka-console-consumer.sh  --bootstrap-server localhost:9093 --from-beginning --topic btc_depth| grep '343,'
	./kafka-console-consumer.sh  --bootstrap-server localhost:9093 --topic btc_future| grep '343,'