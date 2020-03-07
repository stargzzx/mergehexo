---
title: python matplotlib 函数详解，基础画图
date: 2018-12-20 17:16:05
categories:
- python
tags:
- python
- python 第三方库
- matplotlib
---
如题。
<!-- more -->

<br/>

# 函数列表

<br/>

## c

### cla() clf() close()

它们都做不同的事情，因为matplotlib使用分层顺序，其中图形窗口包含可能由许多轴组成的图形。此外，还有来自pyplot接口的函数，还有图类上的方法。

	cla()   # Clear axis即清除当前图形中的当前活动轴。其他轴不受影响。
	clf()   # Clear figure清除所有轴，但是窗口打开，这样它可以被重复使用。
	close() # Close a figure window

看如下代码和例子

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
a = np.arange(1,100)
y1 = a * 7
plt.figure()
plt.subplot(2,1,1)
plt.plot(a,y1)
plt.subplot(2,1,2)
for i in range(4):
    y = a * i
    plt.plot(a,y)
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_3.png %}

添加 cla()

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
a = np.arange(1,100)
y1 = a * 7
plt.figure()
plt.subplot(2,1,1)
plt.plot(a,y1)
plt.subplot(2,1,2)
for i in range(4):
    y = a * i
    plt.cla()
    plt.plot(a,y)
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_4.png %}

替换为 clf()

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
a = np.arange(1,100)
y1 = a * 7
plt.figure()
plt.subplot(2,1,1)
plt.plot(a,y1)
plt.subplot(2,1,2)
for i in range(4):
    y = a * i
    plt.clf()
    plt.plot(a,y)
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_5.png %}

close（）函数还允许指定哪个窗口应该关闭。参数可以是使用figure（number_or_name）创建的窗口的数字或名称。也可以是获得的图形实例，即使用fig = figure（）。如果没有人提出任何论点close()，当前活动的窗口将关闭。 此外，还有语法close（'all'），它关闭所有数字。

Figure的方法

此外，Figure类提供用于清除数字的方法。我将在下面假设fig是Figure:


fig.clf()清除整个数字这个电话相当于plt.clf()只有当fig是目前的数字。

fig.clear()的同义词fig.clf()

请注意，即使“del fig”也不会关闭相关的数字窗口。 据我所知，关闭数字窗口的唯一方法是使用plt.close（fig），综上所述。

## F

### figure()

figure(num=None, figsize=None, dpi=None, facecolor=None, edgecolor=None, frameon=True)
	
	num:图像编号或名称，数字为编号 ，字符串为名称
	figsize:指定figure的宽和高，单位为英寸；
	dpi参数指定绘图对象的分辨率，即每英寸多少个像素，缺省值为100
	facecolor:背景颜色
	edgecolor:边框颜色
	frameon:是否显示边框
	
我对这些参数进行详细的解释。OK，首先 dpi 代表的是每英寸有多少个像素，注意单位是英寸。

另外，我看有的默认缺省位 80 ，有的默认为 100 ，这个要自己注意一下。

	figure = plot.figure(figsize = (4,3))
		# 输出的图像为 像素是 400 * 300
	figure = plot.figure(figsize = (4,3),dpi = 80)
		# 输出的图像为 像素是 320 * 240

## P

### pause()

这个可以暂停会话，单位是毫秒

	plt.pause(1) # 这是暂停 1 秒钟

### plot()

plt.plot(x,y,format_string,**kwargs) 

	x:x轴数据，列表或数组，可选
	y:y轴数据，列表或数组
	format_string:控制曲线的格式字符串，可选
	**kwargs:第二组或更多，(x,y,format_string)
	注：当绘制多条曲线时，各条曲线的x不能省略 
	
format_string：控制曲线的格式字符串，可选，由颜色字符、风格字符和标记字符组成。

{% img /images/python/2_method_14.JPG %}

{% img /images/python/2_method_15.JPG %}


{% img /images/python/2_method_16.JPG %}

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np

a = np.arange(10)
plt.plot(a,a*1.5,'go-',a,a*2.5,'rx',a,a*3.5,'*',a,a*4.5,'b-.')
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_17.png %}

plt.plot(x,y,format_string,**kwargs)

	**kwargs:第二组或更多，(x,y,format_string)
	color:控制颜色，color=’green’ 
	linestyle:线条风格，linestyle=’dashed’ 
	marker:标记风格，marker = ‘o’ 
	markerfacecolor:标记颜色，markerfacecolor = ‘blue’ 
	markersize:标记尺寸，markersize = ‘20’
	
{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
plt.figure()
data = np.random.randn(30).cumsum()
plt.plot(data, 'k--', label = 'Default')
plt.plot(data, 'k-', drawstyle = 'steps-post', label = 'steps-post')
plt.legend(loc='best',labels = ['Default','steps-post']) # 在合适的位置放图示
plt.show()
{% endcodeblock %}	

{% img /images/python/2_method_18.png %}

## T

### text()

text(x=0, y=0, text='', color=None, verticalalignment='baseline', horizontalalignment='left', multialignment=None, fontproperties=None, rotation=None, linespacing=None, rotation_mode=None, usetex=None, wrap=False, **kwargs)[source]

	# 第一个参数是x轴坐标
	# 第二个参数是y轴坐标
	# 第三个参数是要显式的内容
	# alpha 设置字体的透明度
	# family 设置字体
	# size 设置字体的大小
	# style 设置字体的风格
	# wight 字体的粗细
	# bbox 给字体添加框，alpha 设置框体的透明度， facecolor 设置框体的颜色
	
{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
a = np.arange(1,100)
x = 10
y1 = a * 7
plt.figure()
plt.plot(a,y1)
plt.text(-0.05,-0.05,"this is a test %d" % x ,fontdict={'size':20,'color':'red'})
plt.show()
{% endcodeblock %}
{% img /images/python/2_method_6.png %}

## I

### ion() 和 ioff()

在使用matplotlib的过程中，发现不能像matlab一样同时开几个窗口进行比较，于是查询得知了交互模式，但是放在脚本里运行的适合却总是一闪而过，图像并不停留，遂仔细阅读和理解了一下文档，记下解决办法，问题比较简单，仅供菜鸟参考。

python可视化库matplotlib有两种显示模式：

	阻塞（block）模式
	交互（interactive）模式
	
在Python Consol命令行中，默认是交互模式。而在python脚本中，matplotlib默认是阻塞模式。

其中的区别是:

在交互模式下：

	plt.plot(x)或plt.imshow(x)[这是显示图片的函数]是直接出图像，不需要plt.show()
	如果在脚本中使用ion()命令开启了交互模式，没有使用ioff()关闭的话，则图像会一闪而过，并不会常留。要想防止这种情况，需要在plt.show()之前加上ioff()命令。

在阻塞模式下：

	打开一个窗口以后必须关掉才能打开下一个新的窗口。这种情况下，默认是不能像Matlab一样同时开很多窗口进行对比的。
	plt.plot(x)或plt.imshow(x)是直接出图像，需要plt.show()后才能显示图像

总结：
	
	1.在阻塞模式下，plt.plot()和plt.imshow()需要plt.show()才能出图像，而且在一张图片关闭之后才能打开另一张图片，plt.ion()和plt.ioff()就是用来打开和关闭交互模式的函数。 
	2.在交互模式下，可以同时打开多张图片，所以一般可以在一段代码之前plt.ion()打开交互模式，最后再plt.off()关闭，这样同时也处理了交互模式下闪退的问题。

	阻塞模式，即必须利用plt.show()显示图片，且图片关闭之前代码将阻塞在该行。
	交互模式，即plt.plot()后立马显示图片，且不阻塞代码的继续运行。


{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
def y(x):
    return x ** 2
def k(x):
    return np.sin(x)
x = np.linspace(1,5,100)
y1 = x ** 2
y2 = np.sin(x)
# 打开交互模式
plt.ion()
# 同时打开两个窗口显示图片
plt.figure()
plt.plot(x,y(x))
plt.figure()
plt.plot(x,k(x))
# 显示前关掉交互模式
plt.ioff()
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_0.png %}

## S

### scatter()

{% img /images/python/2_method_12.jpg %}

其中散点的形状参数marker如下：

{% img /images/python/2_method_13.jpg %}

{% codeblock %}
N = 10
x = np.random.rand(N)  # 包含10个均匀分布的随机值的横坐标数组，大小[0, 1]
y = np.random.rand(N)  # 包含10个均匀分布的随机值的纵坐标数组
plt.scatter(x, y, alpha=0.6)  # 绘制散点图，透明度为0.6（这样颜色浅一点，比较好看）
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_7.png %}

{% codeblock %}
N = 10
x = np.random.rand(N)
y = np.random.rand(N)
area = np.random.rand(N) * 1000  # 包含10个均匀分布的随机值的面积数组，大小[0, 1000]
fig = plt.figure()
ax = plt.subplot()
ax.scatter(x, y, s=area, alpha=0.5)  # 绘制散点图，面积随机
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_8.png %}

{% codeblock %}
N = 10
x = np.random.rand(N)
y = np.random.rand(N)
x2 = np.random.rand(N)
y2 = np.random.rand(N)
area = np.random.rand(N) * 1000
fig = plt.figure()
ax = plt.subplot()
ax.scatter(x, y, s=area, alpha=0.5)
ax.scatter(x2, y2, s=area, c='green', alpha=0.6)  # 改变颜色
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_9.png %}

{% codeblock %}
N = 10
x = np.random.rand(N)
y = np.random.rand(N)
x2 = np.random.rand(N)
y2 = np.random.rand(N)
x3 = np.random.rand(N)
y3 = np.random.rand(N)
area = np.random.rand(N) * 1000
fig = plt.figure()
ax = plt.subplot()
ax.scatter(x, y, s=area, alpha=0.5)
ax.scatter(x2, y2, s=area, c='green', alpha=0.6)
ax.scatter(x3, y3, s=area, c=area, marker='v', cmap='Reds', alpha=0.7)  # 更换标记样式，另一种颜色的样式
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_10.png %}

图片中的红色倒三角，面积越大的颜色越红。这是因为我们在ax.scatter()中启用了参数cmap，它需要与控制颜色的参数c配合使用。cmap指明调色板的类型，c指明颜色的深浅。

[调色板的类型可见](https://matplotlib.org/users/colormaps.html?highlight=colormap) 

调整散点边界

{% codeblock %}
N = 10
x = [1]
y = [1]
x2 = [1.1]
y2 = [1.1]
x3 = [0.9]
y3 = [0.9]
area = [20000]
fig = plt.figure()
ax = plt.subplot()
ax.scatter(x, y, s=area, alpha=0.5, edgecolors='face')
ax.scatter(x2, y2, s=area, linewidths=[3])
ax.scatter(x3, y3, s=area, alpha=0.5, linewidths=[3], edgecolors='r')
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_11.png %}

### subplot()

可以将多个图放在一张图中。

subplot(numRows, numCols, plotNum)

图表的整个绘图区域被分成 numRows 行和 numCols 列

然后按照从左到右，从上到下的顺序对每个子区域进行编号，左上的子区域的编号为1

plotNum 参数指定创建的 Axes（子图） 对象所在的区域

均与图

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
a = np.arange(1,100)
y1 = a * 7
plt.figure()
plt.subplot(2,1,1)
plt.plot(a,y1)
plt.subplot(2,1,2)
for i in range(4):
    y = a * i
    plt.plot(a,y)
plt.show()
{% endcodeblock %}

{% img /images/python/2_method_1.png %}

不均匀图

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
a = np.arange(1,100)
y1 = a * 7
plt.figure()
plt.subplot(2,1,1)
plt.plot(a,y1)
plt.subplot(2,2,3)
plt.plot(a,a*3)
plt.subplot(2,2,4)
plt.plot(a,a*4)
plt.show()
{% endcodeblock %}

这里解释一下为什么下面的两副图的排列是 2,2 3 和 2,2,4

第一张图，2,1,1 占据一行，为一行一列，但是第二行为 2 列，所以，整个空间就变成了一个 2 * 2 的结构。

虽然，第一张图只是占据了一行，但是却是占据了 2 列，所以，下面的第一张图当然从下标 3 开始

{% img /images/python/2_method_2.png %}


# 形状

## 柱状图

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
plt.figure()
plt.hist(np.random.randn(100), bins  = 20, color = 'k', alpha = 0.3)
plt.show()
{% endcodeblock %}

{% img /images/python/2_3_0.png %}

## 基础图形

{% codeblock %}
import matplotlib.pyplot as plt
import matplotlib.patches as pt
import numpy as np
fig,a = plt.subplots()
# a = plt.figure() 如果 a 是这样得出的话会出错
rect = pt.Rectangle((0.2,0.75), 0.4, 0.15, color = 'r', alpha = 0.3)#左下起点，长，宽，颜色，α
circ = pt.Circle((0.7,0.2), 0.15, color = 'b', alpha = 0.5)#圆心，半径，颜色，α
pgon = pt.Polygon([[0.15, 0.15], [0.35, 0.4], [0.2, 0.6]], color = 'g', alpha = 0.5 )#顶点坐标颜色α
a.add_patch(rect)
a.add_patch(circ)
a.add_patch(pgon)
plt.show()
{% endcodeblock %}

{% img /images/python/2_3_1.png %}

