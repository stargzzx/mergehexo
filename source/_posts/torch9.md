---
title: pytorch | 训练技巧
date: 2020-01-28 00:32:20
categories:
- pytorch
tags:
- pytorch
---
在你看这篇文章之前，我强烈建议你看一下，我之前的一个博文。

里面讲了一些训练技巧，如固定某一层的参数等，因为，后来意识到，这种技巧性的训练方式可能会有很多需要更新，所以，特地开辟了新的博文。

请优先阅读下面的博文。

[pytorch | 微调别人的 model，以及参数相关](https://benpaodewoniu.github.io/2020/01/27/torch8/)

<!-- more -->

# 不同网络层使用不同的学习率进行训练

如果载入的这些参数中，所有参数都更新，但要求一些参数和另一些参数的更新速度（学习率learning rate）不一样，最好知道这些参数的名称都有什么：

	# 载入预训练模型参数后...
	for name, value in model.named_parameters():
	    print(name)
	# 或
	print(model.state_dict().keys())

假设该模型中有encoder，viewer和decoder两部分，参数名称分别是：

	'encoder.visual_emb.0.weight',
	'encoder.visual_emb.0.bias',
	'viewer.bd.Wsi',
	'viewer.bd.bias',
	'decoder.core.layer_0.weight_ih',
	'decoder.core.layer_0.weight_hh',

假设要求encode、viewer的学习率为1e-6， decoder的学习率为1e-4，那么在将参数传入优化器时：

	ignored_params = list(map(id, model.decoder.parameters()))
	base_params = filter(lambda p: id(p) not in ignored_params, model.parameters())
	optimizer = torch.optim.Adam([{'params':base_params,'lr':1e-6},
	                              {'params':model.decoder.parameters()}
	                              ],
	                              lr=1e-4, momentum=0.9)

代码的结果是除decoder参数的learning_rate=1e-4 外，其他参数的额learning_rate=1e-6。

在传入optimizer时，和一般的传参方法torch.optim.Adam(model.parameters(), lr=xxx) 不同，参数部分用了一个list， list的每个元素有params和lr两个键值。如果没有lr则应用Adam的lr属性。Adam的属性除了lr， 其他都是参数所共有的(比如momentum)。
