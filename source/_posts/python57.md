---
title: python 的动态图
date: 2019-03-26 21:56:50
categories:
- python
- 模块
- matplotlib
tags:
- python
- 动态图
- matplotlib
---
学会之后就可以画动态图了。

<!-- more -->

在此之前你可以先看我之前关于动态图的讲解。

[python - matplotlib](https://benpaodewoniu.github.io/2018/05/23/python2/)

## 参考资料

[matplotlib画动态图](https://www.jianshu.com/p/839a6560e307)

## matplotlib 的两种模式

默认为block阻塞，执行后停止，无法动态。

plt.ion()将显示模式转换为interactive模式，也就是交互模式。

	import matplotlib.pyplot as plt
	plt.ion()    # 打开交互模式
	# 同时打开两个窗口显示图片
	plt.figure()  #图片一
	plt.imshow(i1)
	plt.figure()    #图片二
	plt.imshow(i2)
	# 显示前关掉交互模式
	plt.ioff()
	plt.show()

在plt.show()之前一定不要忘了加plt.ioff()，如果不加，界面会一闪而过，并不会停留。

### 在交互模式下

plt.plot(x)或plt.imshow(x)是直接出图像，不需要plt.show()

如果在脚本中使用ion()命令开启了交互模式，没有使用ioff()关闭的话，则图像会一闪而过，并不会常留。要想防止这种情况，需要在plt.show()之前加上ioff()命令。

### 在阻塞模式下

打开一个窗口以后必须关掉才能打开下一个新的窗口。这种情况下，默认是不能像Matlab一样同时开很多窗口进行对比的。

plt.plot(x)或plt.imshow(x)是直接出图像，需要plt.show()后才能显示图像

## show you my code

### 普通

普通的画画方式，可以在我上面的博客链接中查到。

### imshow

	figure = plt.figure()
	plt.ion()
	if i % 100 == 0:
		plt.cla()
		data = sess.run(Gz)[0]
		plt.imshow(data.reshape([28,28]),cmap='Greys')
		plt.pause(0.01)
		plt.ioff()

上面的代码是可以实现动态化的，但是如果加上 plt.show() 就不能实现动态化了。

上面的代码如果去掉 plt.cla() 和 plt.ioff() ，也可以实现动态化，但是我认为 plt.cla() 可以不能去，但是其中细节我也不是很了解。

	figure = plt.figure()
	plt.ion()
	if i % 100 == 0:
		plt.cla()
		data = sess.run(Gz)[0]
		plt.imshow(data.reshape([28,28]),cmap='Greys')
		plt.pause(0.01)
		plt.ioff()
		plt.show()
		
上面的代码就不能实现动态化。

