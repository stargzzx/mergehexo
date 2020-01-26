---
title: pytorch | 模型的自我构建
date: 2020-01-26 20:40:31
categories:
- pytorch
tags:
- pytorch
---
在你看我这篇文章之前，我强烈建议你，先看我之前的文章。

[pytorch | 模型相关](https://benpaodewoniu.github.io/2020/01/26/torch5/)

<!-- more -->

# 参考文章

[PyTorch-网络的创建，预训练模型的加载](https://www.cnblogs.com/wangguchangqing/p/11058525.html)

主要涉及到以下函数的使用

# 函数使用

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

# 构建网络

## 手动一层层构建

torch.nn.Module是所有网络的基类，在Pytorch实现的Model都要继承该类。而且，Module是可以包含其他的Module的，以树形的结构来表示一个网络结构。

简单的定义一个网络Model

{% codeblock %}
class Model(nn.Module):
    def __init__(self):
        super(Model,self).__init__()
        self.conv1 = nn.Conv2d(3,64,3)
        self.conv2 = nn.Conv2d(64,64,3)

    def forward(self,x):
        x = self.conv1(x)
        x = self.conv2(x)
        return x
{% endcodeblock %}

我们必须继承 model 之后，必须要实现 __init__ 和 forward

Model中两个属性conv1和conv2是两个卷积层，在正向传播的过程中，再依次调用这两个卷积层。

除了使用Model的属性来为网络添加层外，还可以使用add_module将网络层添加到网络中。

{% codeblock %}
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
{% endcodeblock %}

add_module(name,layer)在正向传播的过程中可以使用添加时的name来访问改layer。

这样一个个的添加layer，在简单的网络中还行，但是对于负责的网络层很多的网络来说就需要敲很多重复的代码了。 这就需要使用到torch.nn.ModuleList和torch.nn.Sequential。

使用ModuleList和Sequential可以方便添加子网络到网络中，但是这两者还是有所不同的。

## ModuleList

ModuleList是以list的形式保存sub-modules或者网络层，这样就可以先将网络需要的layer构建好保存到一个list，然后通过ModuleList方法添加到网络中。

{% codeblock %}
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
{% endcodeblock %}

使用[nn.Linear(10, 10) for i in range(10)]构建要给Layer的list，然后使用ModuleList添加到网络中，在正向传播的过程中，遍历该list。

更为方便的是，可以提前配置后，所需要的各个Layer的属性，然后读取配置创建list，然后使用ModuleList将配置好的网络层添加到网络中。 以VGG为例：

{% codeblock %}
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
{% endcodeblock %}

读取配置好的网络结构vgg_cfg然后，创建相应的Layer List，使用ModuleList加入到网络中。这样就可以很灵活的创建不同的网络。

这里需要注意的是，ModuleList是将Module加入网络中，需要自己手动的遍历进行每一个Module的forward。

## Sequential

一个时序容器。Modules 会以他们传入的顺序被添加到容器中。当然，也可以传入一个OrderedDict一个时序容器。Modules 会以他们传入的顺序被添加到容器中。当然，也可以传入一个OrderedDict。

Sequential也是一次加入多个Module到网络中中，和ModuleList不同的是，它接受多个Module依次加入到网络中，还可以接受字典作为参数，例如：

{% codeblock %}
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
{% endcodeblock %}

另一个是，Sequential中实现了添加Module的forward，不需要手动的循环调用了。这点相比ModuleList较为方便。

## 总结

常见的有三种方法来添加子Module到网络中


	单独添加一个Module，可以使用属性或者add_module方法。


	ModeluleList可以将一个Module的List加入到网络中，自由度较高，但是需要手动的遍历ModuleList进行forward。


	Sequential按照顺序将将Module加入到网络中，也可以处理字典。 相比于ModuleList不需要自己实现forward

# 遍历网络结构

可以使用以下2对4个方法来访问网络层所有的Modules

	modules() 和 named_modules()
	children() 和 named_children()

## modules方法

简单的定义一个如下网络：

{% codeblock %}
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
{% endcodeblock %}




