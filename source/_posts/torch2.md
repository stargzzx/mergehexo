---
title: pytorch | optimizer.step() 和loss.backward()
date: 2020-01-26 15:38:14
categories:
- pytorch
tags:
- pytorch
---
这个是融合了其他人的观点。

<!-- more -->

<br/>

# 参考资料

<br/>

[Pytorch optimizer.step和loss.backward和scheduler.step的关系与区别](https://blog.csdn.net/xiaoxifei/article/details/87797935)

有人问我optimizer的step为什么不能放在min-batch那个循环之外，还有optimizer.step和loss.backward的区别；那么我想把答案记录下来。

首先需要明确optimzier优化器的作用, 形象地来说，优化器就是需要根据网络反向传播的梯度信息来更新网络的参数，以起到降低loss函数计算值的作用，这也是机器学习里面最一般的方法论。

从优化器的作用出发，要使得优化器能够起作用，需要主要两个东西：
	
1. 优化器需要知道当前的网络或者别的什么模型的参数空间，这也就是为什么在训练文件中，正式开始训练之前需要将网络的参数放到优化器里面，比如使用pytorch的话总会出现类似如下的代码：


	optimizer_G = Adam(model_G.parameters(), lr=train_c.lr_G)   # lr 使用的是初始lr
	optimizer_D = Adam(model_D.parameters(), lr=train_c.lr_D)


2. 需要知道反向传播的梯度信息，我们还是从代码入手，如下所示是Pytorch 中SGD优化算法的step()函数具体写法，具体SGD的写法放在参考部分

{% codeblock %}
def step(self, closure=None):
        """Performs a single optimization step.
        Arguments:
            closure (callable, optional): A closure that reevaluates the model
                and returns the loss.
        """
        loss = None
        if closure is not None:
            loss = closure()
 
        for group in self.param_groups:
            weight_decay = group['weight_decay']
            momentum = group['momentum']
            dampening = group['dampening']
            nesterov = group['nesterov']
 
            for p in group['params']:
                if p.grad is None:
                    continue
                d_p = p.grad.data
                if weight_decay != 0:
                    d_p.add_(weight_decay, p.data)
                if momentum != 0:
                    param_state = self.state[p]
                    if 'momentum_buffer' not in param_state:
                        buf = param_state['momentum_buffer'] = d_p.clone()
                    else:
                        buf = param_state['momentum_buffer']
                        buf.mul_(momentum).add_(1 - dampening, d_p)
                    if nesterov:
                        d_p = d_p.add(momentum, buf)
                    else:
                        d_p = buf
 
                p.data.add_(-group['lr'], d_p)
 
        return loss
{% endcodeblock %}

从上面的代码可以看到step这个函数使用的是参数空间(param_groups)中的grad,也就是当前参数空间对应的梯度，这也就解释了为什么optimzier使用之前需要zero清零一下，因为如果不清零，那么使用的这个grad就得同上一个mini-batch有关，这不是我们需要的结果。

再回过头来看，我们知道optimizer更新参数空间需要基于反向梯度，因此，当调用optimizer.step()的时候应当是loss.backward()的时候，这也就是经常会碰到,如下情况


	total_loss.backward()
	optimizer_G.step()

loss.backward()在前，然后跟一个step。

那么为什么optimizer.step()需要放在每一个batch训练中，而不是epoch训练中，这是因为现在的mini-batch训练模式是假定每一个训练集就只有mini-batch这样大，因此实际上可以将每一次mini-batch看做是一次训练，一次训练更新一次参数空间，因而optimizer.step()放在这里。

scheduler.step（）按照Pytorch的定义是用来更新优化器的学习率的，一般是按照epoch为单位进行更换，即多少个epoch后更换一次学习率，因而scheduler.step()放在epoch这个大循环下。
