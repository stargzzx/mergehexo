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

另外，关于加载model的参数，一共有两个方法，你可以在下面的博文的：读取模型参数 找到两个方法，下面，也展示了另外一个方法。

[pytorch | 模型相关](https://benpaodewoniu.github.io/2020/01/26/torch5/)

## 另外的方法

这是另外一种方法，都是一个原理，但是也记录一下吧。

有时候只需要加载预训练模型的部分参数，可以使用参数名作为过滤条件，如下

	resnet152 = models.resnet152(pretrained=True)
	pretrained_dict = resnet152.state_dict()
	"""加载torchvision中的预训练模型和参数后通过state_dict()方法提取参数
	   也可以直接从官方model_zoo下载：
	   pretrained_dict = model_zoo.load_url(model_urls['resnet152'])"""
	model_dict = model.state_dict()
	# 将pretrained_dict里不属于model_dict的键剔除掉
	pretrained_dict = {k: v for k, v in pretrained_dict.items() if k in model_dict}
	# 更新现有的model_dict
	model_dict.update(pretrained_dict)
	# 加载我们真正需要的state_dict
	model.load_state_dict(model_dict)

model.state_dict()返回一个python的字典对象,将每一层与它的对应参数建立映射关系(如model的每一层的weights及偏置等等)。注意，只有有参数训练的层才会被保存。

上述的加载方式，是按照参数名类匹配过滤的，但是对于一些参数名称无法完全匹配，或者在预训练模型的基础上新添加的一些层，这些层无法从预训练模型中获取参数，需要初始化。

以下是源代码：

下面的 M 类，我去掉了两层。

{% codeblock %}
import torch
import torch.nn as nn
import torch.optim as optim


class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner2 = nn.Linear(2, 1)

    def forward(self, x):
        x = self.liner2(x)
        return x


t = torch.tensor([[2, 1, 3, 5]], dtype=torch.float32)
y = torch.tensor([[1, ]], dtype=torch.float32)

# m.pth 记录了整个网络还有参数信息
model = torch.load('./m.pth')
model_dict = model.state_dict()

# 实例化网络
m = M()
model_dict2 = m.state_dict()

# 得到实例需要的网络层和参数
pretrained_dict = {k: v for k, v in model_dict.items() if k in model_dict2}

# 更新那些层和参数
model_dict2.update(pretrained_dict)
m.load_state_dict(model_dict2)

for name, values in m.named_parameters():
    print(name)
    print(values)
{% endcodeblock %}

就只输出 liner2 的信息了。

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
optimizer = optim.SGD(params, lr=1e-4)

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

## 另外一种阻断传播的方法 detach

detach()的官方说明如下：

>Returns a new Tensor, detached from the current graph.The result will never require gradient.

其源代码如下：

	def detach(self):
	    result = NoGrad()(self)  # this is needed, because it merges version counters
	    result._grad_fn = None
	    return result

假设有模型A和模型B，我们需要将A的输出作为B的输入，但训练时我们只训练模型B. 那么可以这样做：

	input_B = output_A.detach()

它可以使两个计算图的梯度传递断开，从而实现我们所需的功能。简单来说detach就是截断反向传播的梯度流。

在训练网络的时候可能希望保持一部分的网络参数不变，只对其中一部分的参数进行调整；或者值训练部分分支网络，并不让其梯度对主网络的梯度造成影响，这时候我们就需要使用detach()函数来切断一些分支的反向传播。

在这里我用了一个新的 model

	class M(nn.Module):

	    def __init__(self):
	        super(M, self).__init__()
	        self.liner1 = nn.Linear(3, 2)
	        self.liner2 = nn.Linear(2, 2)
	        self.liner3 = nn.Linear(2, 1)

	    def forward(self, x):
	        x = self.liner1(x)
	        x = self.liner2(x)
	        x = self.liner3(x)
	        return x

然后，经过一次计算后，我们保存了网络结构 m.pth

然后我们的代码如下：

{% codeblock %}
import torch
import torch.nn as nn
import torch.optim as optim


class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner1 = nn.Linear(3, 2)
        self.liner2 = nn.Linear(2, 2)
        self.liner3 = nn.Linear(2, 1)

    def forward(self, x):
        x = self.liner1(x)
        x = self.liner2(x)
        x = self.liner3(x.detach())
        return x


t = torch.tensor([[2, 1, 3]], dtype=torch.float32)
y = torch.tensor([[1, ]], dtype=torch.float32)
print(y.shape)

names = ['liner1']

model = torch.load('./m.pth')

for name, value in model.named_parameters():
    print(value)

criterion = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=0.1)

outputs = model(t)
loss = criterion(outputs, y)

loss.backward()
optimizer.step()

for name, value in model.named_parameters():
    print(value)
{% endcodeblock %}

这里面的重点是，在 model M class 中，我们增加了一个 detach() 方法，这个方法会使得前面的参数不变，只改变后面的参数。

下面，我把参数贴一下方便更加快速的理解。

<b style="color: #FF0000;"> 之前的参数</b>

	Parameter containing:
	tensor([[ 0.1912, -0.4310,  0.0671],
	        [ 0.3594, -0.4838,  0.4854]], requires_grad=True)
	Parameter containing:
	tensor([-0.5356, -0.2779], requires_grad=True)
	Parameter containing:
	tensor([[ 0.3054, -0.5407],
	        [-0.4788, -0.1540]], requires_grad=True)
	Parameter containing:
	tensor([-0.3927,  0.2717], requires_grad=True)
	Parameter containing:
	tensor([[-0.1833,  0.6288]], requires_grad=True)
	Parameter containing:
	tensor([0.6480], requires_grad=True)

<b style="color: #FF0000;"> 之后的参数</b>

	Parameter containing:
	tensor([[ 0.1912, -0.4310,  0.0671],
	        [ 0.3594, -0.4838,  0.4854]], requires_grad=True)
	Parameter containing:
	tensor([-0.5356, -0.2779], requires_grad=True)
	Parameter containing:
	tensor([[ 0.3054, -0.5407],
	        [-0.4788, -0.1540]], requires_grad=True)
	Parameter containing:
	tensor([-0.3927,  0.2717], requires_grad=True)
	Parameter containing:
	tensor([[-0.1755,  0.6273]], requires_grad=True)
	Parameter containing:
	tensor([0.6418], requires_grad=True)

## detach_()方法

官网给的解释是：将 Variable 从创建它的 graph 中分离，把它作为叶子节点。

从源码中也可以看出这一点：

将 Variable 的grad_fn 设置为 None，这样，BP 的时候，到这个 Variable 就找不到 它的 grad_fn，所以就不会再往后BP了。

其源代码如下:

	def detach_(self):
	    """Detaches the Variable from the graph that created it, making it a
	    leaf.
	    """
	    self._grad_fn = None
	    self.requires_grad = False

这两个方法的用法和区别:

如果我们有两个网络 A,B, 两个关系是这样的 y=A(x),z=B(y)现在我们想用 z.backward() 来为 B网络的参数来求梯度，但是又不想求 A 网络参数的梯度。我们可以这样：

	# y=A(x), z=B(y) 求B中参数的梯度，不求A中参数的梯度
	# 第一种方法
	y = A(x)
	z = B(y.detach())
	z.backward()
	 
	# 第二种方法
	y = A(x)
	y.detach_()
	z = B(y)
	z.backward()

在这种情况下，detach 和 detach_ 都可以用。但是如果 你也想用 y 来对 A 进行 BP 呢？那就只能用第一种方法了。因为 第二种方法 已经将 A 模型的输出 给 detach（分离）了。

如果将上面的 detach() 换成 detach_() 这个方法，其表现的还是一样的。


举一个例子：

这里暂时先不写例子啦，有时间补上

## 总结

1. requires_grad 和 detach 都可以阻止梯度传播，但是，requires_grad 更加精细，可以阻止具体到某一层的传播，而 detach 一下子阻止好多层的传播

2. 在上面的代码有一个非常重要的就是，我们使用的是 load 之后的 model 进行计算，而不是初始化一个新的例子

# 修改 model ，并且增加新的层

torch 不愧是最符合逻辑的，感觉里面替换层或者增加层都很简单，下面我们分类来进行代码比对。

## 替换层

有的时候我们需要替换一些层，比如有的模型开始用的是 3 通道，但是，我们相用 4 通道，有的最后一层是 1000 个分类，但是，我们只想要 10 个分类。

这个时候只需要替换就可以。

请看代码

我们在最开始的时候网络结构如下

	class M(nn.Module):

	    def __init__(self):
	        super(M, self).__init__()
	        self.liner1 = nn.Linear(3, 2)
	        self.liner2 = nn.Linear(2, 1)

	    def forward(self, x):
	        x = self.liner1(x)
	        x = self.liner2(x)
	        return x

并且，经过一次计算后，我们保存了相关的参数，如下所示：

	('liner1.weight', Parameter containing:
	tensor([[ 0.3026,  0.3919, -0.0346],
	        [-0.0996,  0.4806,  0.4587]], requires_grad=True))
	('liner1.bias', Parameter containing:
	tensor([ 0.4266, -0.4391], requires_grad=True))
	('liner2.weight', Parameter containing:
	tensor([[-0.3627, -0.0493]], requires_grad=True))
	('liner2.bias', Parameter containing:
	tensor([0.4170], requires_grad=True))

我们现在想将 model 的 第一层 liner1 改成 （4，2）

所以我们可以这样来修改，以下是完整的代码演示

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


t = torch.tensor([[2, 1, 3, 5]], dtype=torch.float32)
y = torch.tensor([[1, ]], dtype=torch.float32)
print(y.shape)

model = torch.load('./m.pth')

model.liner1 = nn.Linear(4, 2)

criterion = nn.MSELoss()
optimizer = optim.SGD(model.parameters(), lr=1e-4)

outputs = model(t)
loss = criterion(outputs, y)

for name, value in model.named_parameters():
    print(value)
{% endcodeblock %}

其输入的内容如下所示：

	tensor([[-0.2182,  0.3680,  0.3860,  0.0897],
	        [ 0.4415, -0.3921,  0.4691,  0.4669]], requires_grad=True)
	Parameter containing:
	tensor([ 0.3136, -0.2068], requires_grad=True)
	Parameter containing:
	tensor([[-0.3627, -0.0493]], requires_grad=True)
	Parameter containing:
	tensor([0.4170], requires_grad=True)

可以看出，其参数 liner1 是修改的，但是 liner2 时没修改。

所以，在 torch 中要是想 替换层是非常简单的。

并且，torch 中也预先保留了很多已经训练好的 model ，其修改也和上面一样：

我们先从torchvision中调用基本模型，加载预训练模型，然后，重点来了，将其中的层直接替换为我们需要的层即可：

	resnet = torchvision.models.resnet152(pretrained=True)
	# 原本为1000类，改为10类
	resnet.fc = torch.nn.Linear(2048, 10)

其中使用了pretrained参数，会直接加载预训练模型，内部实现和前文提到的加载预训练的方法一样。因为是先加载的预训练参数，相当于模型中已经有参数了，所以替换掉最后一层即可。OK！

## 增加一些层

增加层也非常简单，直接看代码，下面的 model 我同时实现了，删除层还有增加层。

最开始的 model class 是这样的

	class M(nn.Module):

	    def __init__(self):
	        super(M, self).__init__()
	        self.liner1 = nn.Linear(3, 2)
	        self.liner2 = nn.Linear(2, 1)

	    def forward(self, x):
	        x = self.liner1(x)
	        x = self.liner2(x)
	        return x

<p style="color: #FF0000;">后来，我删除了一层 liner1 并且增加了 liner3</p>

{% codeblock %}
import torch
import torch.nn as nn
import torch.optim as optim


class M(nn.Module):

    def __init__(self):
        super(M, self).__init__()
        self.liner2 = nn.Linear(2, 1)
        self.liner3 = nn.Linear(1, 1)

    def forward(self, x):
        x = self.liner2(x)
        x = self.liner3(x)
        return x


t = torch.tensor([[2, 1, 3, 5]], dtype=torch.float32)
y = torch.tensor([[1, ]], dtype=torch.float32)

# m.pth 记录了整个网络还有参数信息
model = torch.load('./m.pth')
model_dict = model.state_dict()

# 实例化网络
m = M()
model_dict2 = m.state_dict()

# 得到实例需要的网络层和参数
pretrained_dict = {k: v for k, v in model_dict.items() if k in model_dict2}

# 更新那些层和参数
model_dict2.update(pretrained_dict)
m.load_state_dict(model_dict2)

for name, values in m.named_parameters():
    print(name)
    print(values)
{% endcodeblock %}
