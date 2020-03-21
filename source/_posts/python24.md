---
title: matplotlib 画 3D 图像
date: 2018-11-05 22:33:04
categories:
- python
- 模块
- matplotlib
tags:
- python
- tutorial
- matplotlib
---
如题。

<!-- more -->

给出一些比较好玩的例子。

## Rastrigin

![](/images/python/24_0.png)

```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D

fig = plt.figure()
ax = Axes3D(fig)
x = np.arange(-4,4,0.25)
y = np.arange(-4,4,0.25)
x,y = np.meshgrid(x,y) # x-y 平面网络
B = x ** 2 - 10 * np.cos(2 * np.pi * x) + 10
R = y ** 2 - 10 * np.cos(2 * np.pi * y) + 10
Z = B + R
ax.plot_surface(x,y,Z,rstride=1,cstride=1,cmap=plt.get_cmap('rainbow'))
plt.show()
```

![](/images/python/24_1.png)
