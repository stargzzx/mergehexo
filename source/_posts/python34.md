---
title: PIL 关于对图像的长宽处理
date: 2018-12-06 22:22:33
categories:
- python
tags:
- python 第三方库
- python
- PIL
---
额，我觉得这章还是很有必要的，因为有时候这个问题搞得我脑壳疼。

里面的逻辑有点反思维。。。

<!-- more -->

首先，先介绍一下这样一个图，明确图的长和宽。

![](/images/python/34_0.png)

OK，我们下面先上示例图。

![](/images/python/34_1.jpg)

这个图片的长是 320 宽是 510

即

	image.size = (320,510)
	
假设，图片的名字是 1.jpg

## 按照列存像素信息

```python
from PIL import Image
from PIL import ImageDraw
data = Image.open('1.jpg')
print(data.size)
    # (320,510)
data1 = [data.getpixel((x,y)) for y in range(data.size[1]) for x in range(data.size[0])]
print(len(data1))
image = Image.new('RGB',data.size)
draw = ImageDraw.Draw(image)
for j in range(data.size[1]):
    for i in range(data.size[0]):
        image.putpixel([i,j],tuple(data1[j * data.size[0] + i]))
image.save('0.jpg')
```

## 上面关键内容说明

### size

![](/images/python/34_4.png)

### data1

	data1 = [data.getpixel((x,y)) for y in range(data.size[1]) for x in range(data.size[0])]
	
通过读代码我们得知，获取像素点的坐标是 (0,0),(1,0),(2,0)...(319,0),(0,1)...(319,509)

按照我们对坐标系的理解，其获取像素点的顺序应该是如下图所示：

![](/images/python/34_3.png)

但实际上，我们的像素获得点确是下面这个方式

![](/images/python/34_2.png)

让我来自圆其说一下，对于 PIL 的图理解方式来说，坐标的第一个代表的是列，第二个代表的是行。


好吧，就这样理解吧，虽然有点怪怪的。

经过十几分钟过的思考后，我终于想到了一个完美的解决方式，那就是坐标点对应的是坐标系，卧槽，老子可真是天才，看下图。

![](/images/python/34_5.png)

这样坐标系，就能完美的解释上面的问题了。

![](/images/python/34_6.png)

我再写一个代码验证一下。

### 验证猜测

```python
from PIL import Image
from PIL import ImageDraw
image = Image.new('RGB',size=(50,100))
draw = ImageDraw.Draw(image)
draw.line(((0,0),(50,100)),'red',3)
image.save('a.jpg')
```

![](/images/python/34_7.jpg)

### 画图

我们也知道 data1 是按照列的方式来的，但是我们在画图的时候确实按照行关系来的，所以我们要尤为注意各个像素点的信息。

也就是上面的自圆其说了解一下。。。
















