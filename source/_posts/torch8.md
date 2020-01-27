---
title: pytorch | 微调别人的 model，以及参数相关
date: 2020-01-27 15:18:40
categories:
- pytorch
tags:
- pytorch
mathjax: true
---
这个博文是下面两个博文的延伸，以及进阶，如果，你需要，请先看下面的两个博文。

[pytorch | 模型相关](https://benpaodewoniu.github.io/2020/01/26/torch5/)
[pytorch | 模型的自我构建以及其他细节](https://benpaodewoniu.github.io/2020/01/26/torch6/)

<!-- more -->

# 删除层

	pretrained_params = torch.load('Pretrained_Model'）
	model = The_New_Model(xxx)
	model.load_state_dict(pretrained_params.state_dict(), strict=False)

strict=False 使得预训练模型参数中和新模型对应上的参数会被载入，对应不上或没有的参数被抛弃。

代码示例：

## 构建网络，保存

构建一个网络，并进行一次计算后保存网络架构以及参数。

{% codeblock %}
import torch
import torch.nn as nn
import torch.optim as optim

class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner1 = nn.Linear(3, 2)
        self.liner2 = nn.Linear(2, 1)

    def forward(self, x):
        x = self.liner1(x)
        x = self.liner2(x)
        return x

x = torch.tensor([1, 2, 3], dtype=torch.float32)
y = torch.tensor([4], dtype=torch.float32)

m = M()

criterion = nn.MSELoss()
optimizer = optim.SGD(m.parameters(), lr=0.001, momentum=0.9)
outputs = m(x)
loss = criterion(outputs, y)
loss.backward()
optimizer.step()

torch.save(m, 'm.pth')
{% endcodeblock %}

其中我们保存的参数如下面所示

liner1.weight:

	tensor([[ 0.3026,  0.3919, -0.0346],
	        [-0.0996,  0.4806,  0.4587]], requires_grad=True)

liner2.weight:

	tensor([[-0.3627, -0.0493]], requires_grad=True)

liner2.bias

	tensor([0.4170], requires_grad=True)

## 改变网络结构

下面我们改变结构，代码如下：

{% codeblock %}
class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner2 = nn.Linear(2, 1)

    def forward(self, x):
        x = self.liner2(x)
        return x

t = torch.tensor([[2, 1]], dtype=torch.float32)
# 新的 model 实例
m1 = M()

# 将参数放在对应层中
m = torch.load('m.pth')
m1.load_state_dict(m.state_dict(), strict=False)

d = m1(t)
print(d.item())
{% endcodeblock %}

最后的输出是

	-0.3576222360134125

其整个计算过程如下：

$$\left[
\begin{matrix}
-0.3577 \\\\
\end{matrix}
\right] =
\left[
\begin{matrix}
2 & 1 \\\\
\end{matrix}
\right]
\left[
\begin{matrix}
-0.3627 \\\\
-0.0493 \\\\
\end{matrix}
\right] +
0.4170 $$

## 检测是否能继续求导

代码如下：

{% codeblock %}
class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner2 = nn.Linear(2, 1)

    def forward(self, x):
        x = self.liner2(x)
        return x

t = torch.tensor([[2, 1]], dtype=torch.float32)
y = torch.tensor([1], dtype=torch.float32)
# 新的 model 实例
m1 = M()

# 将参数放在对应层中
m = torch.load('m.pth')
m1.load_state_dict(m.state_dict(), strict=False)

criterion = nn.MSELoss()
optimizer = optim.SGD(m1.parameters(), lr=0.001, momentum=0.9)
outputs = m1(t)
loss = criterion(outputs, y)
print(loss.item())
	# 1.8431382179260254

loss.backward()
optimizer.step()
print(m1.liner2.weight)
	# tensor([[-0.3572, -0.0466]], requires_grad=True)
{% endcodeblock %}

可以看出，最后tensor 由

	tensor([[-0.3627, -0.0493]], requires_grad=True)

变成

	tensor([[-0.3572, -0.0466]], requires_grad=True)

说明，是可以继续求导的。

# 有的层参数变，有的不要变

如果载入的这些参数中，有些参数不要求被更新，即固定不变，不参与训练，需要手动设置这些参数的梯度属性为Fasle，并且在optimizer传参时筛选掉这些参数：

	# 载入预训练模型参数后...
	for name, value in model.named_parameters():
	    if name 满足某些条件:
	        value.requires_grad = False

	# setup optimizer
	params = filter(lambda p: p.requires_grad, model.parameters())
	optimizer = torch.optim.Adam(params, lr=1e-4)

代码示例：

{% codeblock %}
import torch
import torch.nn as nn
import torch.optim as optim


class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner1 = nn.Linear(3, 2)
        self.liner2 = nn.Linear(2, 1)

    def forward(self, x):
        x = self.liner1(x)
        x = self.liner2(x)
        return x


t = torch.tensor([[2, 1, 3]], dtype=torch.float32)
y = torch.tensor([[1, ]], dtype=torch.float32)
print(y.shape)

names = ['liner1']

model = torch.load('./m.pth')

for name, value in model.named_parameters():
    print(value)
    if name.split('.')[0] in names:
        value.requires_grad = False

criterion = nn.MSELoss()
params = filter(lambda p: p.requires_grad, model.parameters())
optimizer = optim.SGD(model.parameters(), lr=1e-4)

outputs = model(t)
loss = criterion(outputs, y)

loss.backward()
optimizer.step()

for name, value in model.named_parameters():
    print(value)
{% endcodeblock %}

上面的代码是只更改 liner2 不更改 liner1

例子如下：

之前的参数数值：

liner1.weight

	tensor([[ 0.3026,  0.3919, -0.0346],
	        [-0.0996,  0.4806,  0.4587]], requires_grad=True)

liner1.bias
	
	tensor([ 0.4266, -0.4391], requires_grad=True)

<p style="color: #FF0000;">liner2.weight</p>

	tensor([[-0.3627, -0.0493]], requires_grad=True)

<p style="color: #FF0000;">liner2.bias</p>

	tensor([0.4170], requires_grad=True)

经过更改之后的

liner1.weight

	tensor([[ 0.3026,  0.3919, -0.0346],
    	    [-0.0996,  0.4806,  0.4587]])

liner1.bias
	
	tensor([ 0.4266, -0.4391])

<p style="color: #FF0000;">liner2.weight</p>

	tensor([[-0.3624, -0.0491]], requires_grad=True)

<p style="color: #FF0000;">liner2.bias</p>

	tensor([0.4173], requires_grad=True)

我们可以很明显地看到 liner1 的参数变了。

同样，我们只固定 liner2 也一样可以。

## 疑问

但是，在上面我们看到一段代码是：

	params = filter(lambda p: p.requires_grad, model.parameters())
	optimizer = optim.SGD(params, lr=1e-4)

这个在一些博文中是这样说明的：

>在 optimizer 传入 model parameters 的时候可以用 filter 根据 requires_grad 再过滤一遍。

但是，我在运行代码的过程中把上面那两段话注释掉，换成下面的代码，依然会有相同的结果。

	optimizer = optim.SGD(model.parameters(), lr=1e-4)

其全部的代码如下：

{% codeblock %}
import torch
import torch.nn as nn
import torch.optim as optim


class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner1 = nn.Linear(3, 2)
        self.liner2 = nn.Linear(2, 1)

    def forward(self, x):
        x = self.liner1(x)
        x = self.liner2(x)
        return x


t = torch.tensor([[2, 1, 3]], dtype=torch.float32)
y = torch.tensor([[1, ]], dtype=torch.float32)
print(y.shape)

names = ['liner1']

model = torch.load('./m.pth')

for name, value in model.named_parameters():
    print(value)
    if name.split('.')[0] in names:
        value.requires_grad = False

criterion = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=1e-4)

outputs = model(t)
loss = criterion(outputs, y)

loss.backward()
optimizer.step()

for name, value in model.named_parameters():
    print(value)
{% endcodeblock %}

我的 pytorch 的版本是 1.4.0，torchvision 是 0.5.0

所以，大家在用的时候还是要看好才行。

# 修改 model ，并且增加新的层
