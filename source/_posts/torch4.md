---
title: pytorch | torchvision transform 的讲解
date: 2020-01-26 17:40:38
categories:
- [人工智能,深度学习,框架,pytorch]
tags:
- pytorch
---
pytorch中的torchvision.transforms模块提供了一般的图像转换操作类.

这个类似于批处理命令文件，将对图像的处理统一放在一个流程中，然后调用就好了。

<!-- more -->

[中文文档](https://pytorch-cn.readthedocs.io/zh/latest/torchvision/torchvision-transform/)

首先举一个例子：

```python
transforms.Compose([
     transforms.CenterCrop(10),
     transforms.ToTensor(),
 ])

### class torchvision.transforms.Scale(size, interpolation=2)

#将输入的`PIL.Image`重新改变大小成给定的`size`，`size`是最小边的边长。举个例子，如果原图的`height>width`,那么改变大小后的图片大小是`(size*height/width, size)`。
#**用例:** 
#```python
from torchvision import transforms
from PIL import Image
crop = transforms.Resize(12)

# 这里面一共有三个步骤，一个是从中心截取 10 的图像，然后 totensor 然后再改变大小
img = Image.open('test.jpg')

print(type(img))
print(img.size)

croped_img=crop(img)
print(type(croped_img))
print(croped_img.size)
```

输出

	<class 'PIL.PngImagePlugin.PngImageFile'>
	(10, 10)
	<class 'PIL.Image.Image'>
	(12, 12)

在这里列举几个常见的方法

## CenterCrop(size)

将给定的PIL.Image进行中心切割，得到给定的size，size可以是tuple，(target_height, target_width)。size也可以是一个Integer，在这种情况下，切出来的图片的形状是正方形。

## RandomCrop(size, padding=0)

切割中心点的位置随机选取。size可以是tuple也可以是Integer。

## RandomHorizontalFlip

随机水平翻转给定的PIL.Image,概率为0.5。即：一半的概率翻转，一半的概率不翻转。

## RandomSizedCrop(size, interpolation=2)

先将给定的PIL.Image随机切，然后再resize成给定的size大小。

## Pad(padding, fill=0)

将给定的PIL.Image的所有边用给定的pad value填充。 padding：要填充多少像素 fill：用什么值填充 例子：

```python
from torchvision import transforms
from PIL import Image
padding_img = transforms.Pad(padding=10, fill=0)
img = Image.open('test.jpg')

print(type(img))
print(img.size)

padded_img=padding(img)
print(type(padded_img))
print(padded_img.size)
```

输出

	<class 'PIL.PngImagePlugin.PngImageFile'>
	(10, 10)
	<class 'PIL.Image.Image'>
	(30, 30) #由于上下左右都要填充10个像素，所以填充后的size是(30,30)

对Tensor进行变换

## Normalize(mean, std)

给定均值：(R,G,B) 方差：（R，G，B），将会把Tensor正则化。即：Normalized_image=(image-mean)/std。

## ToTensor

把一个取值范围是[0,255]的PIL.Image或者shape为(H,W,C)的numpy.ndarray，转换成形状为[C,H,W]，取值范围是[0,1.0]的torch.FloadTensor

```python
data = np.random.randint(0, 255, size=300)
img = data.reshape(10,10,3)
print(img.shape)
img_tensor = transforms.ToTensor()(img) # 转换成tensor
```

## ToPILImage

将shape为(C,H,W)的Tensor或shape为(H,W,C)的numpy.ndarray转换成PIL.Image，值不变。

print(img_tensor)

## Lambda(lambd)

使用lambd作为转换器。