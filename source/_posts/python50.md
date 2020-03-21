---
title: python scikit-image skimage
date: 2019-02-16 15:35:53
categories:
- python
- 模块
- scikit-image
tags:
- python
- python 第三方库
- matplotlib
- skimage
- scikit-image
---
这里是 skimage 的教程。

<!-- more -->

## 参考资料

[python图像处理之scikit-image基本用法](https://blog.csdn.net/u012300744/article/details/80083282)

## 介绍

python有很多的数字图像处理相关的包，像

	PIL
	Pillow
	OpenCV
	scikit-image
	等等
	
其中PIL和Pillow只提供最基础的数字图像处理，功能有限；

opencv实际上是一个c++库，只是提供了python接口，

scikit-image是基于scipy的一款图像处理包，它将图片作为numpy数组进行处理，正好与matlab一样，因此，我们最终选择scikit-image进行数字图像处理。

## python库安装

我用的是 conda

	conda install scikit-image
	
scikit-image是基于numpy，因此需要安装numpy和scipy，同时需要安装matplotlib进行图片的实现等。

可以通过如下程序简单测试下相关库是否安装成功

```python
import numpy as np
import scipy as sp
import matplotlib.pyplot as plt
from skimage import io
img = io.imread("./cat.png")
print(img.shape)
plt.imshow(img)
plt.show()		
```

若显示正常，则可以认为相关的库安装成功

## skimage库子模块介绍

skimage库的全称scikit-image Scikit， 是对scipy.ndimage进行了扩展，提供了更多的图片处理功能。skimage包含很多的子模块，各个子模块具有不同的功能，如下

![](/images/python/50_0.png)

当要使用对应的模块中功能函数时，需要通过import导入对应的子模块即可, 若要导入多个子模块时，子模块之间用逗号隔开，如下：

	from skimage import io, data, feature
	
## skimage操作

### 图像读取, 保存与显示

skimage中io子模块提供了相关的功能，同时也提供了一些data模块，其中包含一些示例图片用于练习

导入io子模块的python语句如下:

	from skimage import io
	
### 从外部读取图片并显示

使用 skimage.io.imread(fname) 读取fname指定的图片，

skimage.io.imshow(arr)， 表示显示arr数组表示的图片

```python
from skimage import io
img = io.imread('./cat.png')
io.imshow(img)
io.show()		
```

![](/images/python/50_1.png)

读取单张灰度图片时，使用 skimage.io.imread(fname, as_grey=True) 函数，第一个参数fname表示要显示的图片路径，第二个参数as_grey，是bool类型，默认值False。

```python
from skimage import io
img = io.imread('./cat.png', as_grey=True)
io.imshow(img)
io.show()	
```

![](/images/python/50_2.png)

## skimage自带图片

![](/images/python/50_3.png)

例如

```python
from skimage import io, data
img = data.hubble_deep_field()
io.imshow(img)
io.show()		
```

![](/images/python/50_4.png)

图片名就是对应的函数名，如camera图片对应的函数名为 data.camera()。

注：这些图片存储在skimage的安装目录下，可以通过data_dir把路径打印出来。

	from skimage import data_dir
	print (data_dir)

输出为：

	/usr/local/lib/python3.6/dist-packages/skimage/data
	
## 保存图片

使用 io.imsave(fname, arr) 函数进行保存,

	参数fname: 表示保存的路径和名称
	参数arr：表示需要保存的数组变量

```python
from skimage import io, data

img = data.checkerboard()
io.imshow(img)
io.imsave('checkerboard_copy.jpg', img)		
```

这样，在当前的工作目录下就增加了一个checkerboard_copy.jpg文件。

注：保存图片同时也起到了转换格式的作用，若读取的是png格式图片，当保存为jpg时，则图片从png格式转换为jpg格式图片。

## 获取图片信息

```python
from skimage import io, data

img = data.chelsea()
io.imshow(img)
io.show()

print(type(img))    # 类型
print(img.shape)    # 形状
print(img.shape[0]) # 图片宽度
print(img.shape[1]) # 图片高度
print(img.shape[2]) # 图片通道数
print(img.size)     # 显示总像素个数
print(img.max())    # 最大像素值
print(img.min())    # 最小像素值
print(img.mean())   # 像素平均值		
```

## 图像像素访问与裁剪

图片读入程序后，以numpy数组方式存储，因此对numpy数组的操作，都可以用于图片数组，对数组元素的访问，实际上就是对图片像素点的访问。

### 像素读取

对 彩色图片 的像素点访问方式如下

	img[i, j, c]
		i 表示图片的行数
		j 表示图片的列数
		c 表示图片的通道数(RGB三通道分别对应0, 1, 2)。

坐标从左上角开始

对 灰度图片 的像素点访问方式如下

	gray[i, j]
	
例如， 对data中宇航员图片的B通道中的第20行10列的像素值

```python
from skimage import io, data

img = data.astronaut()
pixel = img[20, 10, 2]
print(pixel)	
```

输出

	69
	
例如，显示红色单通道图片的程序如下

```python
from skimage import io, data

img = data.astronaut()
R = img[:, :, 0]
io.imshow(R)
io.show()		
```

![](/images/python/50_5.png)

### 像素修改

例如，对宇航员图片随机添加椒盐噪声

```python
from skimage import io, data
import numpy as np

img = data.astronaut()

# 随机生成5000个椒盐点
rows, cols, dims = img.shape

for i in range(5000):
    x = np.random.randint(0, rows)
    y = np.random.randint(0, cols)
    img[x, y, :] = 255

io.imshow(img)
io.show()		
```

![](/images/python/50_6.png)

此处，使用numpy中的random来产生随机数， randint(0, cols)表示随机生成一个整数， 范围在0到cols之间。

### 图片裁剪

由于图片是以numpy数组进行存储，因此对于数组的裁剪，就是对图片的裁剪

例如，对宇航员图片进行裁剪

```python
from skimage import io, data

img = data.astronaut()
partial_img = img[50:150, 170:270, :]
io.imshow(partial_img)
io.show()
```

![](/images/python/50_7.png)

对多个像素点进行操作时， 使用数组切片方式进行访问， 切片方式访问的是指定间隔内下标对应的像素点。以下是一些例子

	img[i,:] = im[j,:]      # 将第 j 行的数值赋值给第 i 行
	img[:,i] = 100          # 将第 i 列的所有数值设为 100
	img[:100,:50].sum()     # 计算前 100 行、前 50 列所有数值的和
	img[50:100,50:100]      # 50~100 行，50~100 列（不包括第 100 行和第 100 列）
	img[i].mean()           # 第 i 行所有数值的平均值
	img[:,-1]               # 最后一列
	img[-2,:] (or im[-2])   # 倒数第二行

以下是两个对图片的像素值进行访问和修改的例子

例1： 将宇航员图片进行二值化，像素值大于128的变为1, 否在变为0

```python
from skimage import io, data, color

img = data.astronaut()

img_gray = color.rgb2gray(img)
rows, cols = img_gray.shape

for i in range(rows):
    for j in range(cols):
        if (img_gray[i, j] <= 0.5):
            img_gray[i, j] = 0
        else:
            img_gray[i, j] = 1
io.imshow(img_gray)
io.show()
```

![](/images/python/50_8.png)

例2： 使用color模块的rgb2gray()函数，将彩色三通道图片转换为灰度图片，转换结果为float64类型的数组，范围在[0,1]之间

```python
from skimage import io, data

img = data.astronaut()

img_idx_modified = img[:, :, 0] > 170
print(img_idx_modified)
img[img_idx_modified] = [0, 255, 0]

io.imshow(img)
io.show()
```

![](/images/python/50_9.png)

这个例子先对R通道的所有像素值进行判断，如果大于170，则将这个地方的像素值变为[0,255,0], 即G通道值为255，R和B通道值为0。

## 图像数据类型以及颜色空间转换

### 图像数据类型

在skimage中，一张图片以numpy数组形式存储，数组的数据类型有很多中，相互之间可以转换，数据类型以及取值范围如下表所示

![](/images/python/50_10.png)

一张图片的像素值范围是[0,255], 因此默认类型是unit8, 可用如下代码查看数据类型：

```python
from skimage import io, data
img = data.astronaut()
print(img.dtype.name)
```

输出

	uint8
	
在上面的表中，特别注意的是float类型，它的范围是[-1,1]或[0,1]之间。一张彩色图片转换为灰度图后，它的类型就由unit8变成了float

#### uint8转为float

```python
from skimage import data, img_as_float

img = data.astronaut()
print(img.dtype.name)

dst = img_as_float(img)
print(dst.dtype.name)
```

输出为

	uint8
	float64

#### float转为uint8

```python
from skimage import img_as_ubyte
import numpy as np

img = np.array([0, 0.5, 1], dtype=float)
print(img.dtype.name)

dst = img_as_ubyte(img)
print(dst.dtype.name)
```

输出为：

	float64
	uint8
	
float转为uint8,可能会造成数据损失，因此会有警告

除了如上两种转换以外，还有其他的一些类型转换，如下表:

![](/images/python/50_11.png)

### 颜色空间及转换

除了直接转换可以改变数据类型外，还可以通过图像的颜色空间转换来改变数据类型。

常用的颜色空间有灰度空间、rgb空间、hsv空间和cmyk空间。颜色空间转换以后，图片类型都变成了float型。

所有的颜色空间转换函数，都放在skimage的color模块内

#### RGB转为灰度图

```python
from skimage import io,data,color
img=data.camera()
gray=color.rgb2gray(img)
io.imshow(gray)
io.show()
```

![](/images/python/50_12.png)

其它的转换，用法都是一样的，列举常用的如下：

	skimage.color.rgb2grey(rgb)
	skimage.color.rgb2hsv(rgb)
	skimage.color.rgb2lab(rgb)
	skimage.color.gray2rgb(image)
	skimage.color.hsv2rgb(hsv)
	skimage.color.lab2rgb(lab)

实际上，上面的所有转换函数，都可以用一个函数来代替

	skimage.color.convert_colorspace(arr, fromspace, tospace)

表示将arr从fromspace颜色空间转换到tospace颜色空间。

#### RGB转为HSV

```python
from skimage import io, data, color

img = data.coffee()
hsv = color.convert_colorspace(img, 'RGB', 'HSV')
io.imshow(hsv)
io.show()
```

![](/images/python/50_13.png)

在color模块的颜色空间转换函数中，还有一个比较有用的函数是

skimage.color.label2rgb(arr), 可以根据标签值对图片进行着色。以后的图片分类后着色就可以用这个函数。

#### 将coffee图片分成三类，然后用默认颜色对三类进行着色

```python
from skimage import io,data,color
import numpy as np

img=data.coffee()
gray=color.rgb2gray(img)
rows,cols=gray.shape

labels=np.zeros([rows,cols])
for i in range(rows):
    for j in range(cols):
        if(gray[i,j]<0.4):
            labels[i,j]=0
        elif(gray[i,j]<0.75):
            labels[i,j]=1
        else:
            labels[i,j]=2
dst=color.label2rgb(labels)

io.imshow(dst)
io.show()
```

![](/images/python/50_14.png)












