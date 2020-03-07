---
title: PIL Image 模块函数详解
date: 2018-12-03 16:58:39
categories:
- python
tags:
- PIL
- pillow
- python
- tutorial
---
这里是介绍 pillow Image 模块的章节。

OK，enjoy it。

<!-- more -->

# Image 模块

## C

### convert()

[Python图像处理库PIL中图像格式转换](https://blog.csdn.net/icamera0/article/details/50843172)

Convert()函数有三种形式的定义，它们定义形式如下：

	im.convert(mode) ⇒ image
	im.convert(“P”, **options) ⇒ image
	im.convert(mode, matrix) ⇒ image
	
使用不同的参数，将当前的图像转换为新的模式，并产生新的图像作为返回值。

PIL中有九种不同模式。分别为1，L，P，RGB，RGBA，CMYK，YCbCr，I，F。

本文我采用的示例图像是图像处理中经典的lena照片。分辨率为512x512的lena图片如下：

{% img /images/python/27_1.jpg %}

#### 一、模式“RGB”转换为其他不同模式

模式“1”

模式“1”为二值图像，非黑即白。但是它每个像素用8个bit表示，0表示黑，255表示白。下面我们将lena图像转换为“1”图像。

例子：

{% codeblock %}
>>>from PIL import Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena.mode
'RGB'
>>> lena.getpixel((0,0))
(197, 111, 78)
>>> lena_1 = lena.convert("1")
>>> lena_1.mode
'1'
>>> lena_1.size
(512, 512)
>>>lena_1.getpixel((0,0))
255
>>> lena_1.getpixel((10,10))
255
>>>lena_1.getpixel((10,120))
0 
>>>lena_1.getpixel((130,120))
255
{% endcodeblock %}

图像lena_1的模式为“1”，分辨率为512x512，如下：

{% img /images/python/27_2.jpg %}

2、 模式“L”

模式“L”为灰色图像，它的每个像素用8个bit表示，0表示黑，255表示白，其他数字表示不同的灰度。在PIL中，从模式“RGB”转换为“L”模式是按照下面的公式转换的：

L = R * 299/1000 + G * 587/1000+ B * 114/1000

下面我们将lena图像转换为“L”图像。

例子：

{% codeblock %}
>>> from PIL importImage
>>> lena = Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena.mode
'RGB'
>>> lena.getpixel((0,0))
(197, 111, 78)
>>> lena_L =lena.convert("L")
>>> lena_L.mode
'L'
>>> lena_L.size
(512, 512)
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena_L.getpixel((0,0))
132
{% endcodeblock %}

对于第一个像素点，原始图像lena为(197, 111, 78)，其转换为灰色值为：

197 *299/1000 + 111 * 587/1000 + 78 * 114/1000 = 132.952，PIL中只取了整数部分，即为132。

转换后的图像lena_L如下：

{% img /images/python/27_3.jpg %}

3、 模式“P”

模式“P”为8位彩色图像，它的每个像素用8个bit表示，其对应的彩色值是按照调色板查询出来的。

下面我们使用默认的调色板将lena图像转换为“P”图像。

例子：

{% codeblock %}
>>> from PIL importImage
>>> lena = Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena.mode
'RGB'
>>> lena.getpixel((0,0))
(197, 111, 78)
>>> lena_P =lena.convert("P")
>>> lena_P.mode
'P'
>>>lena_P.getpixel((0,0))
62
{% endcodeblock %}

转换后的图像lena_P如下：

{% img /images/python/27_4.png %}

4、 模式“RGBA”

模式“RGBA”为32位彩色图像，它的每个像素用32个bit表示，其中24bit表示红色、绿色和蓝色三个通道，另外8bit表示alpha通道，即透明通道。

下面我们将模式为“RGB”的lena图像转换为“RGBA”图像。

例子： 

{% codeblock %}
>>> from PIL import Image
>>>lena = Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>>lena.mode
'RGB'
>>>lena.getpixel((0,0))
(197,111, 78)
>>>lena_rgba = lena.convert("RGBA")
>>>lena_rgba.mode
'RGBA'
>>>lena_rgba.getpixel((0,0))
(197,111, 78, 255)
>>>lena_rgba.getpixel((0,1))
(196,110, 77, 255)
>>>lena.getpixel((0,0))
(197,111, 78)
>>>lena.getpixel((0,1))
(196,110, 77)
{% endcodeblock %}

从实例中可以看到，使用当前这个方式将“RGB”图像转为“RGBA”图像时，alpha通道全部设置为255，即完全不透明。

转换后的图像lena_rgba如下：

{% img /images/python/27_5.jpg %}

5、 模式“CMYK”

模式“CMYK”为32位彩色图像，它的每个像素用32个bit表示。模式“CMYK”就是印刷四分色模式，它是彩色印刷时采用的一种套色模式，利用色料的三原色混色原理，加上黑色油墨，共计四种颜色混合叠加，形成所谓“全彩印刷”。

四种标准颜色是：C：Cyan = 青色，又称为‘天蓝色’或是‘湛蓝’M：Magenta = 品红色，又称为‘洋红色’；Y：Yellow = 黄色；K：Key Plate(blacK) = 定位套版色（黑色）。

下面我们将模式为“RGB”的lena图像转换为“CMYK”图像。

例子：

{% codeblock %}
>>>from PIL import Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_cmyk =lena.convert("CMYK")
>>> lena_cmyk.mode
'CMYK'
>>>lena_cmyk.getpixel((0,0))
(58, 144, 177, 0)
>>> lena_cmyk.getpixel((0,1))
(59, 145, 178, 0)
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena.getpixel((0,1))
(196, 110, 77)
{% endcodeblock %}

从实例中可以得知PIL中“RGB”转换为“CMYK”的公式如下：

	C = 255 - R
	M = 255 - G
	Y = 255 - B
	K = 0
	
由于该转换公式比较简单，转换后的图像颜色有些失真。

转换后的图像lena_cmyk如下：

{% img /images/python/27_6.jpg %}

6、 模式“YCbCr”

模式“YCbCr”为24位彩色图像，它的每个像素用24个bit表示。YCbCr其中Y是指亮度分量，Cb指蓝色色度分量，而Cr指红色色度分量。人的肉眼对视频的Y分量更敏感，因此在通过对色度分量进行子采样来减少色度分量后，肉眼将察觉不到的图像质量的变化。

模式“RGB”转换为“YCbCr”的公式如下：

	Y= 0.257*R+0.504*G+0.098*B+16
	Cb = -0.148*R-0.291*G+0.439*B+128
	Cr = 0.439*R-0.368*G-0.071*B+128

下面我们将模式为“RGB”的lena图像转换为“YCbCr”图像。

例子：

{% codeblock %}
>>>from PIL import Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_ycbcr =lena.convert("YCbCr")
>>>lena_ycbcr.mode
'YCbCr'
>>>lena_ycbcr.getpixel((0,0))
(132, 97, 173)
>>>lena.getpixel((0,0))
(197, 111, 78)
{% endcodeblock %}

按照公式，Y = 0.257*197+0.564*111+0.098*78+16= 136.877

	Cb= -0.148*197-0.291*111+0.439*78+128= 100.785
	Cr = 0.439*197-0.368*111-0.071*78+128 = 168.097

由此可见，PIL中并非按照这个公式进行“RGB”到“YCbCr”的转换。

转换后的图像lena_ycbcr如下：

{% img /images/python/27_7.jpg %}

7、 模式“I”

模式“I”为32位整型灰色图像，它的每个像素用32个bit表示，0表示黑，255表示白，(0,255)之间的数字表示不同的灰度。在PIL中，从模式“RGB”转换为“I”模式是按照下面的公式转换的：

	I = R * 299/1000 + G * 587/1000 + B * 114/1000

下面我们将模式为“RGB”的lena图像转换为“I”图像。

例子：

{% codeblock %}
>>> from PIL import Image
>>>lena = Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>>lena.getpixel((0,0))
(197,111, 78)
>>>lena.getpixel((0,1))
(196,110, 77)
>>> lena_I =lena.convert("I")
>>> lena_I.mode
'I'
>>>lena_I.getpixel((0,0))
132
>>>lena_I.getpixel((0,1))
131
>>> lena_L =lena.convert("L")
>>>lena_L.getpixel((0,0))
132
>>>lena_L.getpixel((0,1))
131
{% endcodeblock %}

从实验的结果看，模式“I”与模式“L”的结果是完全一样，只是模式“L”的像素是8bit，而模式“I”的像素是32bit。

8、 模式“F”

模式“F”为32位浮点灰色图像，它的每个像素用32个bit表示，0表示黑，255表示白，(0,255)之间的数字表示不同的灰度。在PIL中，从模式“RGB”转换为“F”模式是按照下面的公式转换的：

F = R * 299/1000+ G * 587/1000 + B * 114/1000

下面我们将模式为“RGB”的lena图像转换为“F”图像。

例子：

{% codeblock %}
>>>from PIL import Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena.getpixel((0,1))
(196, 110, 77)
>>> lena_F =lena.convert("F")
>>> lena_F.mode
'F'
>>>lena_F.getpixel((0,0))
132.95199584960938
>>>lena_F.getpixel((0,1))
131.95199584960938
{% endcodeblock %}

模式“F”与模式“L”的转换公式是一样的，都是RGB转换为灰色值的公式，但模式“F”会保留小数部分，如实验中的数据。

####  二、其他不同模式转换为“RGB”模式


模式“RGB”为24位彩色图像，它的每个像素用24个bit表示，分别表示红色、绿色和蓝色三个通道。

在PIL中，对于彩色图像，open后都会转换为“RGB”模式，然后该模式可以转换为其他模式，比如“1”、“L”、“P”和“RGBA”，这几种模式也可以转换为“RGB”模式。

1、 模式“1”转换为模式“RGB”

模式“RGB”转换为模式“1”以后，像素点变成黑白两种点，要么是0，要么是255。而从模式“1”转换成“RGB”时，“RGB”的三个通道都是模式“1”的像素值的拷贝。

例子：

{% codeblock %}
>>> from PILimport Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_1 =lena.convert("1")
>>> lena_1_rgb =lena_1.convert("RGB")
>>> lena.getpixel((0,0))
(197, 111, 78)
>>>lena_1.getpixel((0,0))
255
>>>lena_1_rgb.getpixel((0,0))
(255, 255, 255)
{% endcodeblock %}

2、 模式“L”转换为模式“RGB”

模式“RGB”转换为模式“L”以后，像素值为[0,255]之间的某个数值。而从模式“L”转换成“RGB”时，“RGB”的三个通道都是模式“L”的像素值的拷贝。

例子：

{% codeblock %}
>>> from PIL import Image
>>> lena = Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_L =lena.convert("L")
>>> lena_L_rgb =lena_L.convert("RGB")
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena_L.getpixel((0,0))
132
>>>lena_rgb.getpixel((0,0))
(132, 132, 132)
{% endcodeblock %}

3、 模式“P”转换为模式“RGB”

模式“RGB”转换为模式“P”以后，像素值为[0,255]之间的某个数值，但它为调色板的索引值，其最终还是彩色图像。从模式“P”转换成“RGB”时，“RGB”的三个通道会变成模式“P”的像素值索引的彩色值。

例子：

{% codeblock %}
>>> from PIL import Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_P =lena.convert("P")
>>> lena_P_rgb =lena_P.convert("RGB")
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena_P.getpixel((0,0))
62
>>>lena_P_rgb.getpixel((0,0))
(204, 102, 51)
{% endcodeblock %}

4、 模式“RGBA”转换为模式“RGB”

模式“RGB”转换为模式“RGBA”以后，图像从三通道变成了四通道，其R、G、B三个通道的数值没有变化，新增的alpha通道均为255，表示不透明。从模式“RGBA”转换成“RGB”时，“RGB”的三个通道又变回原来的数值。

例子：

{% codeblock %}
>>> from PILimport Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_RGBA =lena.convert("RGBA")
>>> lena_RGBA_rgb= lena_RGBA.convert("RGB")
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena_RGBA.getpixel((0,0))
(197, 111, 78, 255)
>>>lena_RGBA_rgb.getpixel((0,0))
(197, 111, 78)
{% endcodeblock %}

5、 模式“CMYK”转换为模式“RGB”

模式“RGB”转换为模式“CMYK”以后，图像从三通道变成了四通道，其C、M、Y三个通道的数值是通过之前的公式计算得到，K通道被直接赋值为0。

	C = 255 - R
	M = 255 - G
	Y = 255 - B
	K = 0

从模式“CMYK”转换成“RGB”时，“RGB”的三个通道又变回原来的数值，这是无损的转换。

	R = 255 - C
	G = 255 - M
	B = 255 - Y

例子：

{% codeblock %}
>>> from PIL import Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_CMYK =lena.convert("CMYK")
>>> lena_CMYK_rgb= lena_CMYK.convert("RGB")
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena_CMYK.getpixel((0,0))
(58, 144, 177, 0)
>>>lena_CMYK_rgb.getpixel((0,0))
(197, 111, 78)
{% endcodeblock %}

6、 模式“YCbCr”转换为模式“RGB”

模式“RGB”转换为模式“YCbCr”，通常都是使用下面的公式计算，但PIL中并没有严格按照这个公式进行转换。

	Y= 0.257*R+0.564*G+0.098*B+16
	Cb = -0.148*R-0.291*G+0.439*B+128
	Cr = 0.439*R-0.368*G-0.071*B+128

从模式“YCbCr”转换成“RGB”时，通常是按照下面的公式计算，但PIL中并没有严格按照这个公式进行转换。

	R= 1.164*(Y-16)+1.596*(Cr-128)
	G = 1.164*(Y-16)-0.392*(Cb-128)-0.813*(Cr-128)
	B = 1.164*(Y-16)+2.017*(Cb-128)

例子：

{% codeblock %}
>>> from PILimport Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_YCbCr =lena.convert("YCbCr")
>>> lena_YCbCr_rgb= lena_YCbCr.convert("RGB")
>>>lena.getpixel((0,0))
(197, 111, 78) 
>>>lena_YCbCr.getpixel((0,0))
(132, 97, 173)
>>>lena_YCbCr_rgb.getpixel((0,0))
(195, 110, 77)
{% endcodeblock %}

7、 模式“I”转换为模式“RGB”

模式“RGB”转换为模式“I”，将三通道变成了单通道，使用下面的公式计算获得像素值：

I = R * 299/1000+ G * 587/1000 + B * 114/1000

从模式“I”转换成“RGB”时，“RGB”的三个通道都是模式“I”的像素值的拷贝。

例子：

{% codeblock %}
>>> from PILimport Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_I =lena.convert("I")
>>> lena_I_rgb =lena_I.convert("RGB")
>>>lena.getpixel((0,0))
(197, 111, 78)
>>>lena_I.getpixel((0,0))
132
>>>lena_I_rgb.getpixel((0,0))
(132, 132, 132)
{% endcodeblock %}

8、 模式“F”转换为模式“RGB”

模式“RGB”转换为模式“F”，将彩色图像变成了32位浮点灰色图像。在PIL中，从模式“RGB”转换为“F”模式是按照下面的公式转换的：

	F = R * 299/1000+ G * 587/1000 + B * 114/1000

从模式“F”转换成“RGB”时，“RGB”的三个通道都是模式“F”的像素值整数部分的拷贝。

例子：

{% codeblock %}
>>> from PILimport Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_F =lena.convert("F")
>>> lena_F_rgb =lena_F.convert("RGB")
>>>lena.getpixel((0,0))
(197, 111, 78)
>>> lena_F.getpixel((0,0))
132.95199584960938
>>>lena_F_rgb.getpixel((0,0))
(132, 132, 132)
{% endcodeblock %}

#### 三、调色板图像的转换

在PIL中，将“RGB”图像转换为“P”模式图像时，有对应的convert()函数定义，如下：

im.convert(“P”,**options) ⇒ image

这个定义将模式固定为“P”，后面可以带几个可选参数。它们分别为：dither，palette和colors。

参数dither用于控制颜色抖动。默认是FLOYDSTEINBERG，不使能该功能，则赋值为NONE。

参数palette用于控制调色板的产生。默认是WEB，这是标准的216色的“web palette”。要使用优化的调色板，则赋值为ADAPTIVE。

参数colors用于控制调色板颜色数目。当参数palette为ADAPTIVE时，colors数值表示调色板的颜色数目。默认是最大值，即256种颜色。

使用默认值，将“RGB”转换为“P”模式图像后如下：

参数dither默认为FLOYDSTEINBERG；如果不开启颜色抖动功能，则赋值为NONE。其转换结果如下：

参数palette默认是WEB。要使用优化的调色板，则赋值为ADAPTIVE。其转换结果为：

当参数palette为ADAPTIVE时，colors数值表示调色板的颜色数目。默认值为256。当colors设置为10时，其转换结果为：

上述转换代码如下：

{% codeblock %}
>>> from PILimport Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena_P_dither= lena.convert("P", dither = Image.NONE)
>>> lena_P_palette= lena.convert("P", palette = Image.ADAPTIVE)
>>>lena_P_palette_colors = lena.convert("P", palette = Image.ADAPTIVE,colors = 10)
{% endcodeblock %}

#### 四、带矩阵的模式转换

模式转换函数convert()的第三种定义如下：

im.convert(mode,matrix) ⇒ image

这种定义只适合将一个“RGB”图像转换为“L”或者“RGB”图像，不能转换为其他模式的图像。变量matrix为4或者16元组。

例子：下面的例子将一个RGB图像（根据ITU-R709线性校准，使用D65亮度）转换到CIE XYZ颜色空间：

{% codeblock %}
>>>from PIL import Image
>>> lena =Image.open("D:\\Code\\Python\\test\\img\\lena.jpg")
>>> lena.mode
'RGB'
>>> rgb2xyz= (
             0.412453,0.357580, 0.180423, 0,
                       0.212671,0.715160, 0.072169, 0,
                       0.019334,0.119193, 0.950227, 0 )
>>>lena_L = lena.convert("L", rgb2xyz)
>>>lena_rgb = lena.convert("RGB", rgb2xyz)
{% endcodeblock %}

图像lena_L如下：

{% img /images/python/27_8.jpg %}

图像lena_rgb如下：

{% img /images/python/27_9.jpg %}


### alpha_composite()

alpha_composite(im1, im2)：在im1对象上的透明层复合im2，返回一个Image对象 

	im1：Image对象1
	im2：Image对象2

im1和im2的size要相同。且im1和im2的mode都必须是RGBA

{% codeblock %}
from PIL import Image, ImageDraw
im1 = Image.new('RGBA',size=(300,300),color='red')
im2 = Image.new('RGBA',size=(300,300))
draw = ImageDraw.Draw(im2)
draw.line((0,0) +im2.size,'green',10)
image = Image.alpha_composite(im1,im2)
image.save('aaa.png')
	# 这个 image 最后的图象是红色背景，绿色的线
{% endcodeblock %}

## G

### getpixel()

im.getpixel((x, y))	得到某个像素点的颜色

{% codeblock %}
from PIL import Image, ImageDraw
im1 = Image.new('RGBA',size=(300,300),color='red')
rgb = im1.getpixel((1,1))
print(rgb)
    # (255, 0, 0, 255)
{% endcodeblock %}


## N

### new

Image.new(mode,size) ⇒ image


Image.new(mode, size, color) ⇒ image

一共有两种模式。

使用给定的变量mode和size生成新的图像。Size是给定的宽/高二元组，这是按照像素数来计算的。对于单通道图像，变量color只给定一个值；对于多通道图像，变量color给定一个元组（每个通道对应一个值）。在版本1.1.4及其之后，用户也可以用颜色的名称，比如给变量color赋值为“red”。如果没有对变量color赋值，图像内容将会被全部赋值为0（图像即为黑色）。如果变量color是空，图像将不会被初始化，即图像的内容全为0。这对向该图像复制或绘制某些内容是有用的。

{% img /images/python/27_0.png %}

{% codeblock %}
from PIL import Image
im= Image.new("RGB", (128, 128), "#FF0000")
im.show()
	# 输出的是一个 128 * 128 的红色矩阵
	# 不设置 color 的话就是黑色
{% endcodeblock %}


## O

### open

Image.open(fp, mode =’r’ )：打开图片文件，返回一个Image对象 

	fp：图片路径
	mode：模式。如果给出，必须是r
	