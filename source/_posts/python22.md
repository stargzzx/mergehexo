---
title: 将 matplotlib 的 figure 转化为 numpy 的 array
date: 2018-10-29 22:05:01
categories:
- python
tags:
- python 第三方库
- python
- tutorial
- numpy
- matplotlib
---
如题。

<!-- more -->

[原文章](https://blog.csdn.net/aa846555831/article/details/52372884)


我还没有具体试过，但是先 mark 一下。

最近在写深度学习的代码的时候遇到一个问题，在python中，我们常用matplotlib库进行数据处理和分析，而对于结果，如果想要进行其他的操作，通常是希望能转换为numpy库的ndarray.

在查阅了网上很多资料后，主要有两种方法，第一种方法我试了并没有效果，但还是放在下面。

{% codeblock %}
import matplotlib.pyplot as plt
import numpy as np

# Make a random plot...
fig = plt.figure()
fig.add_subplot(111)

# If we haven't already shown or saved the plot, then we need to
# draw the figure first...
fig.canvas.draw()

# Now we can save it to a numpy array.
data = np.fromstring(fig.canvas.tostring_rgb(), dtype=np.uint8, sep='')
data = data.reshape(fig.canvas.get_width_height()[::-1] + (3,))
{% endcodeblock %}

思路是用matplotlib库中的canvas方法将其转换，但我在使用中发现出现全白的情况，有可能和前面的HSV处理有关，但始终找不到解决办法，

第二种方法更为简单，利用matplotlib的savefig()方法，将图片先保存在本地，再用PIL或openCV方法将图片读入，确实可用，但会发现效率太低了，尤其我是要处理视频流。一个很好的改进方法是利用StringIO方法，不是让其内容保存在本地磁盘，而是在内存是开辟一个缓冲，这样效率大大提高了。

上代码：

{% codeblock %}
import matplotlib.pyplot as plt
import pylab
import imageio
import skimage.io
import cv2
from cStringIO import StringIO
import PIL
#申请缓冲地址
buffer_ = StringIO()#using buffer,great way!
#保存在内存中，而不是在本地磁盘，注意这个默认认为你要保存的就是plt中的内容
plt.savefig(buffer_,format = 'png')
buffer_.seek(0)
#用PIL或CV2从内存中读取
dataPIL = PIL.Image.open(buffer_)
#转换为nparrary，PIL转换就非常快了,data即为所需
data = np.asarray(dataPIL)
cv2.imshow('image', data)
#释放缓存    
buffer_.close()
{% endcodeblock %}

总结，实测方法二效率还是非常高的。这也是目前找到的两种比较靠谱的方法。