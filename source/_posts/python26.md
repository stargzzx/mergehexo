---
title: 用 PIL 对像素点进行操作
date: 2018-12-01 20:57:49
categories:
- python
tags:
- python
- PIL
---
这是一个很神奇的代码，OK，下面请看效果图。

<!-- more -->

```python
import matplotlib.pyplot as plt
import numpy as np
import time
from PIL import Image

data = Image.open('image.jpg')
data = np.array(data)
data_array = []
for i in range(data.shape[0]):
    for j in range(data.shape[1]):
        if np.sum(data[i][j]) == 255 * 3:
            data_array.append(0)
        else:
            data_array.append(1)
x = data.shape[0]
y = data.shape[1]
t = Image.new('RGB', (x, y))
for i in range(0, y):
    for j in range(0, x):
        if data_array[i * y + j] == 1:
            t.putpixel([j, i], tuple(np.array([57,57,57])))
        else:
            t.putpixel([j, i], tuple(np.array([255, 255, 255])))
t.save('max/licong.jpg')
```
