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

# 一个简单的结构

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
class M(nn.Module):
    def __init__(self):
        super(M, self).__init__()
        self.liner1 = nn.Linear(2, 2)

    def forward(self, x):
        return self.liner1(x)

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

这里有一个特别注意的点就是，我们虽然可以从文件中直接读取参数和网络结构，但是，在文件中还是要放一下我们构建模型的那个 model。

# 一个持续更新的代码

{% codeblock %}
import torch
import torch.nn as nn
import numpy as np
import matplotlib.pyplot as plt


# Hyper-parameters
input_size = 1
output_size = 1
num_epochs = 60
learning_rate = 0.001

# Toy dataset
x_train = np.array([[3.3], [4.4], [5.5], [6.71], [6.93], [4.168], 
                    [9.779], [6.182], [7.59], [2.167], [7.042], 
                    [10.791], [5.313], [7.997], [3.1]], dtype=np.float32)

y_train = np.array([[1.7], [2.76], [2.09], [3.19], [1.694], [1.573], 
                    [3.366], [2.596], [2.53], [1.221], [2.827], 
                    [3.465], [1.65], [2.904], [1.3]], dtype=np.float32)

# Linear regression model
model = nn.Linear(input_size, output_size)

# Loss and optimizer
criterion = nn.MSELoss()
optimizer = torch.optim.SGD(model.parameters(), lr=learning_rate)  

# Train the model
for epoch in range(num_epochs):
    # Convert numpy arrays to torch tensors
    inputs = torch.from_numpy(x_train)
    targets = torch.from_numpy(y_train)

    # Forward pass
    outputs = model(inputs)
    loss = criterion(outputs, targets)
    
    # Backward and optimize
    optimizer.zero_grad()
    loss.backward()
    optimizer.step()
    
    if (epoch+1) % 5 == 0:
        print ('Epoch [{}/{}], Loss: {:.4f}'.format(epoch+1, num_epochs, loss.item()))

# Plot the graph
predicted = model(torch.from_numpy(x_train)).detach().numpy()
plt.plot(x_train, y_train, 'ro', label='Original data')
plt.plot(x_train, predicted, label='Fitted line')
plt.legend()
plt.show()

# Save the model checkpoint
torch.save(model.state_dict(), 'model.ckpt')
{% endcodeblock %}

optimizer.zero_grad() 之所以有这个方法，请看我下面的博文。

[pytorch | optimizer.step 和loss.backward](https://benpaodewoniu.github.io/2020/01/26/torch2/)








