---
title: pytorch | 模型的自我构建以及其他细节（参数、结构等）
date: 2020-01-26 20:40:31
categories:
- [人工智能,深度学习,框架,pytorch]
tags:
- pytorch
---
在你看我这篇文章之前，我强烈建议你，先看我之前的文章。

[pytorch | 模型相关](https://benpaodewoniu.github.io/2020/01/26/torch5/)

<!-- more -->

<br/>

# 参考文章

<br/>

[PyTorch-网络的创建，预训练模型的加载](https://www.cnblogs.com/wangguchangqing/p/11058525.html)

主要涉及到以下函数的使用

<br/>

# 函数使用

<br/>

## 模型创建

	add_module
	ModulesList
	Sequential

## 访问模型的各个子模块

	modules()
	named_modules()
	children()
	named_children()

## 网络参数的遍历
	parameters()
	named_parameters()

## 模型的保存与加载

	save()
	load()
	state_dict() 

<br/>

# 构建网络

<br/>

## 手动一层层构建

torch.nn.Module是所有网络的基类，在Pytorch实现的Model都要继承该类。而且，Module是可以包含其他的Module的，以树形的结构来表示一个网络结构。

简单的定义一个网络Model

```python
class Model(nn.Module):
    def __init__(self):
        super(Model,self).__init__()
        self.conv1 = nn.Conv2d(3,64,3)
        self.conv2 = nn.Conv2d(64,64,3)

    def forward(self,x):
        x = self.conv1(x)
        x = self.conv2(x)
        return x
```

我们必须继承 model 之后，必须要实现 __init__ 和 forward

Model中两个属性conv1和conv2是两个卷积层，在正向传播的过程中，再依次调用这两个卷积层。

除了使用Model的属性来为网络添加层外，还可以使用add_module将网络层添加到网络中。

```python
class Model(nn.Module):
    def __init__(self):
        super(Model,self).__init__()
        self.conv1 = nn.Conv2d(3,64,3)
        self.conv2 = nn.Conv2d(64,64,3)

        self.add_module("maxpool1",nn.MaxPool2d(2,2))
        self.add_module("covn3",nn.Conv2d(64,128,3))
        self.add_module("conv4",nn.Conv2d(128,128,3))

    def forward(self,x):
        x = self.conv1(x)
        x = self.conv2(x)
        x = self.maxpool1(x)
        x = self.conv3(x)
        x = self.conv4(x)
        return x
```

add_module(name,layer)在正向传播的过程中可以使用添加时的name来访问改layer。

这样一个个的添加layer，在简单的网络中还行，但是对于负责的网络层很多的网络来说就需要敲很多重复的代码了。 这就需要使用到torch.nn.ModuleList和torch.nn.Sequential。

使用ModuleList和Sequential可以方便添加子网络到网络中，但是这两者还是有所不同的。

## ModuleList

ModuleList是以list的形式保存sub-modules或者网络层，这样就可以先将网络需要的layer构建好保存到一个list，然后通过ModuleList方法添加到网络中。

```python
class MyModule(nn.Module):
    def __init__(self):
        super(MyModule,self).__init__()

        # 构建layer的list
        self.linears = nn.ModuleList([nn.Linear(10, 10) for i in range(10)])

    def forward(self,x):

        # 正向传播，使用遍历每个Layer
        for i, l in enumerate(self.linears):
            x = self.linears[i // 2](x) + l(x)

        return x
```

使用[nn.Linear(10, 10) for i in range(10)]构建要给Layer的list，然后使用ModuleList添加到网络中，在正向传播的过程中，遍历该list。

更为方便的是，可以提前配置后，所需要的各个Layer的属性，然后读取配置创建list，然后使用ModuleList将配置好的网络层添加到网络中。 以VGG为例：

```python
vgg_cfg = [64, 64, 'M', 128, 128, 'M', 256, 256, 256, 'C', 512, 512, 512, 'M',
           512, 512, 512, 'M']

def vgg(cfg, i, batch_norm=False):
    layers = []
    in_channels = i
    for v in cfg:
        if v == 'M':
            layers += [nn.MaxPool2d(kernel_size=2, stride=2)]
        elif v == 'C':
            layers += [nn.MaxPool2d(kernel_size=2, stride=2, ceil_mode=True)]
        else:
            conv2d = nn.Conv2d(in_channels, v, kernel_size=3, padding=1)
            if batch_norm:
                layers += [conv2d, nn.BatchNorm2d(v), nn.ReLU(inplace=True)]
            else:
                layers += [conv2d, nn.ReLU(inplace=True)]
            in_channels = v
    return layers

class Model1(nn.Module):
    def __init__(self):
        super(Model1,self).__init__()

        self.vgg = nn.ModuleList(vgg(vgg_cfg,3))

    def forward(self,x):

        for l in self.vgg:
            x = l(x)
m1 = Model1()
print(m1)
```

读取配置好的网络结构vgg_cfg然后，创建相应的Layer List，使用ModuleList加入到网络中。这样就可以很灵活的创建不同的网络。

这里需要注意的是，ModuleList是将Module加入网络中，需要自己手动的遍历进行每一个Module的forward。

## Sequential

一个时序容器。Modules 会以他们传入的顺序被添加到容器中。当然，也可以传入一个OrderedDict一个时序容器。Modules 会以他们传入的顺序被添加到容器中。当然，也可以传入一个OrderedDict。

Sequential也是一次加入多个Module到网络中中，和ModuleList不同的是，它接受多个Module依次加入到网络中，还可以接受字典作为参数，例如：

```python
# Example of using Sequential
        model = nn.Sequential(
                  nn.Conv2d(1,20,5),
                  nn.ReLU(),
                  nn.Conv2d(20,64,5),
                  nn.ReLU()
                )

# Example of using Sequential with OrderedDict
model = nn.Sequential(OrderedDict([
    ('conv1', nn.Conv2d(1,20,5)),
    ('relu1', nn.ReLU()),
    ('conv2', nn.Conv2d(20,64,5)),
    ('relu2', nn.ReLU())
    ]))
```

另一个是，Sequential中实现了添加Module的forward，不需要手动的循环调用了。这点相比ModuleList较为方便。

## 总结

常见的有三种方法来添加子Module到网络中


	单独添加一个Module，可以使用属性或者add_module方法。


	ModeluleList可以将一个Module的List加入到网络中，自由度较高，但是需要手动的遍历ModuleList进行forward。


	Sequential按照顺序将将Module加入到网络中，也可以处理字典。 相比于ModuleList不需要自己实现forward

<br/>

# 遍历网络结构

<br/>

可以使用以下2对4个方法来访问网络层所有的Modules

	modules() 和 named_modules()
	children() 和 named_children()

## modules方法

简单的定义一个如下网络：

```python
class Model(nn.Module):
    def __init__(self):
        super(Model,self).__init__()
        self.conv1 = nn.Conv2d(in_channels=3,out_channels=64,kernel_size=3)
        self.conv2 = nn.Conv2d(64,64,3)
        self.maxpool1 = nn.MaxPool2d(2,2)

        self.features = nn.Sequential(OrderedDict([
            ('conv3', nn.Conv2d(64,128,3)),
            ('conv4', nn.Conv2d(128,128,3)),
            ('relu1', nn.ReLU())
        ]))

    def forward(self,x):
        x = self.conv1(x)
        x = self.conv2(x)
        x = self.maxpool1(x)
        x = self.features(x)

        return x
```

modules()方法，返回一个包含当前模型所有模块的迭代器，这个是递归的返回网络中的所有Module。使用如下语句

	m = Model()
    for idx,m in enumerate(m.modules()):
        print(idx,"-",m)

其结果为：

	0 - Model(
	  (conv1): Conv2d(3, 64, kernel_size=(3, 3), stride=(1, 1))
	  (conv2): Conv2d(64, 64, kernel_size=(3, 3), stride=(1, 1))
	  (maxpool1): MaxPool2d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
	  (features): Sequential(
	    (conv3): Conv2d(64, 128, kernel_size=(3, 3), stride=(1, 1))
	    (conv4): Conv2d(128, 128, kernel_size=(3, 3), stride=(1, 1))
	    (relu1): ReLU()
	  )
	)
	1 - Conv2d(3, 64, kernel_size=(3, 3), stride=(1, 1))
	2 - Conv2d(64, 64, kernel_size=(3, 3), stride=(1, 1))
	3 - MaxPool2d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
	4 - Sequential(
	  (conv3): Conv2d(64, 128, kernel_size=(3, 3), stride=(1, 1))
	  (conv4): Conv2d(128, 128, kernel_size=(3, 3), stride=(1, 1))
	  (relu1): ReLU()
	)
	5 - Conv2d(64, 128, kernel_size=(3, 3), stride=(1, 1))
	6 - Conv2d(128, 128, kernel_size=(3, 3), stride=(1, 1))
	7 - ReLU()

输出结果解析：

	0-Model 整个网络模块
	1-2-3-4 为网络的4个子模块，注意4 - Sequential仍然包含有子模块
	5-6-7为模块4 - Sequential的子模块

可以看出modules()是递归的返回网络的各个module，从最顶层直到最后的叶子module。

named_modules()的功能和modules()的功能类似，不同的是它返回内容有两部分:module的名称以及module。

```python
model = torch.load('./m.pth')

for name, value in model.named_parameters():
    print(name)
    print(value)
```

    liner1.weight
    Parameter containing:
    tensor([[ 0.3026,  0.3919, -0.0346],
            [-0.0996,  0.4806,  0.4587]], requires_grad=True)
    liner1.bias
    Parameter containing:
    tensor([ 0.4266, -0.4391], requires_grad=True)
    liner2.weight
    Parameter containing:
    tensor([[-0.3627, -0.0493]], requires_grad=True)
    liner2.bias
    Parameter containing:
    tensor([0.4170], requires_grad=True)


## 从文件中读取

```python
import torch
import torch.nn as nn
import torch.optim as optim


class M(nn.Module):
    def __init__(self):
        super(M, self).__init__()
        self.liner1 = nn.Linear(2, 2)

    def forward(self, x):
        return self.liner1(x)

if __name__ == '__main__':
    m = torch.load('./m.pth')
    # print(m.liner1.weight)
    for idx, m in enumerate(m.modules()):
        print(idx, "-", m)
```

从代码中我们知道，虽然可以从文件中读取，但是依然要给定定义的 model 类。

## children()方法

和modules()不同，children()只返回当前模块的子模块，不会递归子模块。

	for idx,m in enumerate(m.children()):
	        print(idx,"-",m)

其输出为：

	0 - Conv2d(3, 64, kernel_size=(3, 3), stride=(1, 1))
	1 - Conv2d(64, 64, kernel_size=(3, 3), stride=(1, 1))
	2 - MaxPool2d(kernel_size=2, stride=2, padding=0, dilation=1, ceil_mode=False)
	3 - Sequential(
	  (conv3): Conv2d(64, 128, kernel_size=(3, 3), stride=(1, 1))
	  (conv4): Conv2d(128, 128, kernel_size=(3, 3), stride=(1, 1))
	  (relu1): ReLU()
	)

子模块3-Sequential仍然有子模块，children()没有递归的返回。

named_children()和children()的功能类似，不同的是其返回两部分内容：模块的名称以及模块本身。

# 网络的参数

方法parameters()返回一个包含模型所有参数的迭代器。一般用来当作optimizer的参数。

	for p in m.parameters():
	        print(type(p.data),p.size())

其输出为：

	<class 'torch.Tensor'> torch.Size([128, 64, 3, 3])
	<class 'torch.Tensor'> torch.Size([128])
	<class 'torch.Tensor'> torch.Size([128, 128, 3, 3])
	<class 'torch.Tensor'> torch.Size([128])

## 在训练的过程中获得参数

```python
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
print(d.weight)
	# 这个来输出某一层的权重
```

## 读取网络文件来输出权重

	m = torch.load('./m.pth')
	print(m.liner1.weight)
		这个也要给定 model 类，具体的可以参照下面的博客：一个简单的结构

[pytorch | 几个比较简单的代码示例](https://benpaodewoniu.github.io/2020/01/26/torch7/)

你也可以根据上面的遍历网络结构的 named_modules() 来直接输出。不过，其还是要引入 model 类，哈哈！！！尴尬。。。

## 多层定义

```python
class net(nn.Module):
    def __init__(self):
        super().__init__()
	    self.c1 = nn.Sequential(
	    nn.Conv2d(3,16,5,1,2), 
	    nn.ReLU()
	        ) 
	    self.c2 = nn.Sequential(
	        nn.Conv2d(16,32,5,1,2), 
	        nn.ReLU(),
	    )
	    self.fc = nn.Linear(2097152,2)
	def forward(self,x):
	    x = self.c1(x)
	    x = self.c2(x)
	    x = x.view(x.size(0), -1) 
	    x = self.fc(x) 
	    return x   

代码省略

model.fc
>>
Linear(in_features=1048576, out_features=2, bias=True)
model.c1[0]
>>
Conv2d(3, 16, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
#获得某层的权重
model.c1[0].weight
>>
Parameter containing:
tensor([[[[ 2.7182e-03, -8.7767e-03,  3.2988e-02, -1.0006e-01, -1.1177e-01],
          [-2.9155e-02, -6.2152e-02,  4.1465e-02, -4.5812e-02,  6.7885e-02],
      	...
      	...
model.c1[0].parameters()为该层的参数，包含梯度等等
```

因为其包含网络中的所有的权值矩阵参数以及偏置参数。 对网络进行训练时需要将parameters()作为优化器optimizer的参数。

	optimizer = torch.optim.SGD(m1.parameters(),lr = args.lr,momentum=args.momentum,weight_decay=args.weight_decay)

parameters()返回网络的所有参数，主要是提供给optimizer用的。而要取得网络某一层的参数或者参数进行一些特殊的处理（如fine-tuning)，则使用named_parameters()更为方便些。

named_parameters()返回参数的名称及参数本身，可以按照参数名对一些参数进行处理。
