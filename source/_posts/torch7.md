---
title: pytorch | 几个比较简单的代码示例
date: 2020-01-26 21:52:01
categories:
- pytorch
tags:
- pytorch
---
这是几个很简单的代码示例，方便你快速入门。

<!-- more -->

# 只是一个简单的流程

{% codeblock %}
x = torch.randn(10, 3)
y = torch.randn(10, 2)

# Build a fully connected layer.
linear = nn.Linear(3, 2)
print ('w: ', linear.weight)
print ('b: ', linear.bias)

# Build loss function and optimizer.
criterion = nn.MSELoss()
optimizer = torch.optim.SGD(linear.parameters(), lr=0.01)

# Forward pass.
pred = linear(x)

# Compute loss.
loss = criterion(pred, y)
print('loss: ', loss.item())

# Backward pass.
loss.backward()

# Print out the gradients.
print ('dL/dw: ', linear.weight.grad) 
print ('dL/db: ', linear.bias.grad)

# 1-step gradient descent.
optimizer.step()

# You can also perform gradient descent at the low level.
# linear.weight.data.sub_(0.01 * linear.weight.grad.data)
# linear.bias.data.sub_(0.01 * linear.bias.grad.data)

# Print out the loss after 1-step gradient descent.
pred = linear(x)
loss = criterion(pred, y)
print('loss after 1 step optimization: ', loss.item())
{% endcodeblock %}

# 有一个简单的结构

{% codeblock %}
import torch
import torch.nn as nn
import torch.optim as optim


class M(nn.Module):
    def __init__(self):
        super(M, self).__init__()
        self.liner1 = nn.Linear(2, 2)

    def forward(self, x):
        return self.liner1(x)


x = torch.tensor([3., 4.], dtype=torch.float32)
y = torch.tensor([3., 9.], dtype=torch.float32)
m = M()
criterion = nn.MSELoss()
optimizer = optim.SGD(m.parameters(), lr=0.001, momentum=0.9)

outputs = m(x)
loss = criterion(outputs, y)
loss.backward()
optimizer.step()
d = m.liner1
torch.save(m, './m.pth')
{% endcodeblock %}

这个代码有一个简单的结构。

里面那个模型，中间有一层 liner1 的参数如下：

	[ 0.1022, -0.6525],
	[ 0.0175,  0.5175]

下面是载入模型的代码。

{% codeblock %}
x = torch.tensor([3., 4.], dtype=torch.float32)
y = torch.tensor([1., 1.], dtype=torch.float32)
m = torch.load('./m.pth')
print(m.liner1.weight)
	# 这个也输出
	# [ 0.1022, -0.6525],
    # [ 0.0175,  0.5175]
criterion = nn.MSELoss()
outputs = m(x)
print(outputs)
loss = criterion(outputs, y)
print(loss)
{% endcodeblock %}









