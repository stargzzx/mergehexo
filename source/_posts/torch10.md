---
title: pytorch | 使用 gpu 和 cpu 进行计算
date: 2020-01-29 18:32:39
categories:
- [人工智能,深度学习,框架,pytorch]
tags:
- pytorch
---
如果合理的使用 gpu 和 cpu 计算，也是深度学习的入门项目之一。

<!-- more -->

这个暂时先放一下

在看这篇文章之前，希望你看我的一份代码，是在下面的博文中的：一个简单的具有完整网络结构的例子 。里面会根据是否有 gpu 来考虑如何计算。

[pytorch | 几个比较简单的代码示例](https://benpaodewoniu.github.io/2020/01/26/torch7/)

<br/>

# model=model.to(device)

<br/>

这代表将模型加载到指定设备上。

其中，<i style="color: #FF0000;">device=torch.device("cpu")</i> 代表的使用cpu，而 <i style="color: #FF0000;">device=torch.device("cuda")</i> 则代表的使用GPU。

当我们指定了设备之后，就需要将模型加载到相应设备中，此时需要使用model=model.to(device)，将模型加载到相应的设备中。

将由GPU保存的模型加载到CPU上。

