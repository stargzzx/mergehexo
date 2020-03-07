---
title: python - matplotlib
date: 2018-05-23 14:10:44
categories:
- python
tags:
- python
- tutorial
- matplotlib
---
Here is the tutorial of python's matplotlib.
<!-- more -->

# 基本使用方法

## 简介

matplotlib是python中强大的画图模块，按官网上的图例，基本能做出各种各样美观的图表，但python缺省并不带这个模块，需要自己安装呵

	pip install -i http://pypi.douban.com/simple matplotlib

同时也会相应自主安装 numpy

## 基本用法

我们用不到 matplotlib 的所有功能，基本上只是使用一小部分，所以只需要：

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
x = np.linspace(-1,1,50)
y = 2 * x + 1
plt.plot(x,y)	
plt.show()
{% endcodeblock %}

{% img /images/python/2_0.png %}

## figure 的使用

figure(num=None, figsize=None, dpi=None, facecolor=None, edgecolor=None, frameon=True)

	num:图像编号或名称，数字为编号 ，字符串为名称
	figsize:指定figure的宽和高，单位为英寸；
	dpi参数指定绘图对象的分辨率，即每英寸多少个像素，缺省值为80
	facecolor:背景颜色
	edgecolor:边框颜色
	frameon:是否显示边框

想要画出多个图像

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
x = np.linspace(-1, 1, 50)
y1 = 2 * x + 1
y2 = x ** 2
plt.figure()
plt.plot(x, y1)
plt.figure(num = 3,figsize=(8,5))
	# num 是命名这个图片 figsize 是设置大小
plt.plot(x, y2)
plt.plot(x,y1,color = 'red',linewidth = 1.0,linestyle = '--')
	# 在一幅图中做两条线
	# linestyle 是线段类型，比如这个就是虚线
plt.show()
{% endcodeblock %}

一共表现为两张图

{% img /images/python/2_0.png %}

{% img /images/python/2_1.png %}

## 设置坐标

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
x = np.linspace(-1, 1, 50)
y1 = 2 * x + 1
y2 = x ** 2
plt.figure()
plt.plot(x, y2)
plt.plot(x,y1,color = 'red',linewidth = 1.0,linestyle = '--')

# 设置 x y 轴
plt.xlim((-1,2))
plt.ylim((-2,3))

# 设置坐标轴名字
plt.xlabel('i am x')
plt.ylabel('i am y')

# 换间隔 比如原来从 -1 到 1 是 -1 0 1 可以改为 -1 -0.5  。。。 1，y 同理
new_ticks = np.linspace(-1,2,5)
plt.xticks(new_ticks)

# 给特定的点设置标签
plt.yticks(
	[-2.-1.8,-1,1.22,3],
	# 在标签前面加 r 然后字符前后加 $ 可以转换字体 另外中间空格需要加 \（因为这是正则的形式，空格需要转义）
	# 如果要加特殊符号 比如 阿尔法 可以是 \alpha 其他特殊符号可以搜一下
	[r'$really \ bad$', 'bad', r'$normal\ \alpha$', 'good', 'really good']
)
plt.show()
{% endcodeblock %}

{% img /images/python/2_2.png %}

{% codeblock %}
#修改坐标轴位置
import matplotlib.pyplot as plt
import numpy as np
x = np.linspace(-1, 1, 50)
y1 = 2 * x + 1
y2 = x ** 2
plt.figure()
plt.plot(x, y2)
plt.plot(x,y1,color = 'red',linewidth = 1.0,linestyle = '--')

# 设置 x y 轴
plt.xlim((-1,2))
plt.ylim((-2,3))

# 设置坐标轴名字
plt.xlabel('i am x')
plt.ylabel('i am y')

# 换间隔 比如原来从 -1 到 1 是 -1 0 1 可以改为 -1 -0.5  。。。 1，y 同理
new_ticks = np.linspace(-1,2,5)
plt.xticks(new_ticks)

# 给特定的点设置标签
plt.yticks(
	[-2.-1.8,-1,1.22,3],
	# 在标签前面加 r 然后字符前后加 $ 可以转换字体 另外中间空格需要加 \（因为这是正则的形式，空格需要转义）
	# 如果要加特殊符号 比如 阿尔法 可以是 \alpha 其他特殊符号可以搜一下
	[r'$really \ bad$', 'bad', r'$normal\ \alpha$', 'good', 'really good']
)

# gca 得到现在的坐标抽
ax = plt.gca()

# 边框，有四条，分别是上下左右
ax.spines['right'].set_color('none')
ax.spines['top'].set_color('none')

# 设置坐标点位置，比如原来的原点是 (0,0)，现在的原点是（0，-1）
ax.xaxis.set_ticks_position('bottom')
ax.yaxis.set_ticks_position('left')
ax.spines['bottom'].set_position(('data',-1))
ax.spines['left'].set_position(('data',0))
plt.show()
{% endcodeblock %}

{% img /images/python/2_3.png %}

## legend 图例

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
x = np.linspace(-1, 1, 50)
y1 = 2 * x + 1
y2 = x ** 2
plt.figure()
plt.xlim((-1, 2))
plt.ylim((-2, 3))
plt.xlabel('i am x')
plt.ylabel('i am y')
new_ticks = np.linspace(-1, 2, 5)
plt.xticks(new_ticks)
plt.yticks(
    [-2. - 1.8, -1, 1.22, 3],
    [r'$really \ bad$', 'bad', r'$normal\ \alpha$', 'good', 'really good']
)

#对线段进行解释说明
plt.plot(x, y2,label = 'up')
plt.plot(x, y1, color='red', linewidth=1.0, linestyle='--',label = 'down')

# 参数有很多，其中有个是 loc 位置 当 loc = ‘best’时，会自动找寻最佳位置
# label的选择是就近原则，如下不会打出 up,down 只会打出 li cong
plt.legend(loc = 'best',labels = ['li','cong'])
plt.show()
{% endcodeblock %}

{% img /images/python/2_4.png %}

## 动图

{% codeblock %}
import numpy as np
import matplotlib.pyplot as plt
import time
number = np.linspace(0,5,100)
y = np.sin(number)
plt.plot(number,y)
for i in range(200):
    l = plt.scatter(i, 5, s=200, lw=0, c='red', alpha=0.5)
    plt.pause(0.1)
    if 'l' in globals():
        l.remove()
plt.show()
{% endcodeblock %}

#接口

## pyplot

pyplot是一个收集一些函数的模块，这些函数允许以函数式方式使用matplotlib。




# 小技巧

## 去掉坐标系

这两段代码效果一样。

{% codeblock %}
plt.figure()
plt.xticks([])
plt.yticks([])
data = np.arange(12)
plt.plot(data,data*2)
plt.show()
{% endcodeblock %}

注意，类似的这些操作若想起作用，需要将其置于 plt.show() 之前，plt.imshow() 之后

{% codeblock %}
plt.figure()
frame = plt.gca()
# y 轴不可见
frame.axes.get_yaxis().set_visible(False)
# x 轴不可见
frame.axes.get_xaxis().set_visible(False)
data = np.arange(12)
plt.plot(data,data*2)
plt.show()
{% endcodeblock %}

{% img /images/python/2_4_0.png %}

## 去掉边界

{% codeblock %}
plt.figure()
frame = plt.gca()
# y 轴不可见
frame.axes.get_yaxis().set_visible(False)
# x 轴不可见
frame.axes.get_xaxis().set_visible(False)
plt.axis('off')
data = np.arange(12)
plt.plot(data,data*2)
plt.show()
{% endcodeblock %}

{% img /images/python/2_4_1.png %}

注意，类似的这些操作若想起作用，需要将其置于 plt.show() 之前，plt.imshow() 之后

## 无限动图

效果图如下：

{% img /images/python/2_4_2.gif %}

Matplotlib中默认是使用阻塞模式。看一下这里用到的matplotlib中的几个函数：

	plt.ion()：打开交互模式
	plt.ioff()：关闭交互模式
	plt.clf()：清除当前的Figure对象
	plt.cla()：清除当前的Axes对象
	plt.pause()：暂停功能
	
了解了以上几个函数之后，就可以很方便的画出动态图了。原理很简单，就是一个“画图-->清理-->画图”的循环，注意这中间的pause暂停。

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
def test():
	plt.figure(figsize=(8, 6), dpi=80)
	plt.ion()
	for i in range(100):
		plt.cla()
		x = np.linspace(-np.pi + 0.1 * i, np.pi + 0.1 * i, 256, endpoint=True)
		y = np.cos(x)
		plt.plot(x,y)
		plt.pause(0.01)
		plt.ioff()
		plt.show()
test()
{% endcodeblock %}

让人感到奇怪的是，即便是将 ion() 和 ioff() 注释掉后，还是原来的效果。。。

真让人头大

## 解决中文乱码

### win下的解决方案

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
plt.figure()
plt.text(0.5,0.3,"李丛123")
plt.show()
{% endcodeblock %}

{% img /images/python/2_4_3.png %}

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np
plt.rcParams['font.sans-serif']=['SimHei'] #用来正常显示中文标签
plt.rcParams['axes.unicode_minus']=False #用来正常显示负号
#有中文出现的情况，需要u'内容'
plt.figure()
plt.text(0.5,0.3,u"李丛123")
plt.show()
{% endcodeblock %}

{% img /images/python/2_4_4.png %}

### macbook下的解决方案

由于Mac系统自带有中文字库，Arial Unicode MS即为其中一种。不需要安装字库，不需要修改配置文件。

{% codeblock %}
import matplotlib.pyplot as plt
plt.rcParams["font.family"] = 'Arial Unicode MS'
{% endcodeblock %}

