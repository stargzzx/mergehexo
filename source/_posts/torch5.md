---
title: pytorch | 模型相关（保存、载入、修改等）
date: 2020-01-26 18:02:44
categories:
- pytorch
tags:
- pytorch
---
模型以及参数是否能够合理地保存下来，是能否进行产品迭代的保证。

<!-- more -->

# 参考资料

[PyTorch 预训练模型，保存，读取和更新模型参数以及多 GPU 训练模型](https://blog.csdn.net/Code_Mart/article/details/88254444)

# 保存模型参数

PyTorch 中保存模型的方式有许多种：

{% codeblock %}
# 保存整个网络，载入后可以直接用
torch.save(model, PATH)
# 保存网络中的参数, 速度快，占空间少
torch.save(model.state_dict(),PATH)
# 选择保存网络中的一部分参数或者额外保存其余的参数
torch.save({'state_dict': model.state_dict(), 'fc_dict':model.fc.state_dict(),
            'optimizer': optimizer.state_dict(),'alpha': loss.alpha, 'gamma': loss.gamma},
            PATH)
{% endcodeblock %}

# 读取模型参数

同样的，PyTorch 中读取模型参数的方式也有许多种：

{% codeblock %}
# 读取整个网络
model = torch.load(PATH)

# 读取 Checkpoint 中的网络参数
model.load_state_dict(torch.load(PATH))

# 若 Checkpoint 中的网络参数与当前网络参数有部分不同,有以下两种方式进行加载:
# 1. 利用字典的 update 方法进行加载
Checkpoint = torch.load(Path)
model_dict = model.state_dict()
model_dict.update(Checkpoint)
model.load_state_dict(model_dict)
# 2. 利用 load_state_dict() 的 strict 参数进行部分加载
model.load_state_dict(torch.load(PATH), strict=False)
{% endcodeblock %}

如何利用前面的参数呢？我以 官方 的一段代码进行阐述，当然，利用参数有两种情况

	自己创建的 model
	别人创建的 model

## 自己的 model

以下代码参照

[TRAINING A CLASSIFIER](https://pytorch.org/tutorials/beginner/blitz/cifar10_tutorial.html)

{% codeblock %}
class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(3, 6, 5)
        self.pool = nn.MaxPool2d(2, 2)
        self.conv2 = nn.Conv2d(6, 16, 5)
        self.fc1 = nn.Linear(16 * 5 * 5, 120)
        self.fc2 = nn.Linear(120, 84)
        self.fc3 = nn.Linear(84, 10)

    def forward(self, x):
        x = self.pool(F.relu(self.conv1(x)))
        x = self.pool(F.relu(self.conv2(x)))
        x = x.view(-1, 16 * 5 * 5)
        x = F.relu(self.fc1(x))
        x = F.relu(self.fc2(x))
        x = self.fc3(x)
        return x

# 经过一系列的计算，保存参数

PATH = './cifar_net.pth'
torch.save(net.state_dict(), PATH)

# 使用 参数
net = Net()
net.load_state_dict(torch.load(PATH))

outputs = net(images)

	#dataiter = iter(testloader)
	#images, labels = dataiter.next()

# 输出结果
_, predicted = torch.max(outputs, 1)
print('Predicted: ', ' '.join('%5s' % classes[predicted[j]]
                              for j in range(4)))
{% endcodeblock %}

在这个代码中，我们可以看出，我们得预先知道整个网络的结构才行。

## 不使用 model，直接使用模型

关于这个代码，请大家看下面博文的第2个例子:一个简单的结构

[pytorch | 几个比较简单的代码示例](https://benpaodewoniu.github.io/2020/01/26/torch7/)

## 别人的 model

微调网络结构

Pytorch 提供了许多 Pre-Trained Model on ImageNet，仅需调用 torchvision.models 即可，具体细节可查看官方文档。

往往我们需要对 Pre-Trained Model 进行相应的修改，以适应我们的任务。这种情况下，我们可以先输出 Pre-Trained Model 的结构，确定好对哪些层修改，或者添加哪些层，接着，再将其修改即可。

因为有预感在这个章节中可能有很多内容需要撰写更新，所以，决定开设新的博文，请移步到下面的博文。
