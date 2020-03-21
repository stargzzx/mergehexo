---
title: PIL 和 matplotlib 相结合，将图片输入到内存并读取
date: 2018-12-01 10:00:08
categories:
- [python,模块,PIL]
- [python,模块,matplotlib]
tags:
- python
- PIL
- matplotlib
---
我们经常会遇到这样一个场景，我们要生成并处理大量的图片。

<!-- more -->

```python
import matplotlib.pyplot as plt
import pylab
import numpy as np
from PIL import Image
from io import BytesIO

buffer_ = BytesIO()
a = np.arange(1,10,0.01)
y = a ** 2
figure = plt.figure()
plt.plot(a,y)
figure.savefig(buffer_,format = 'jpg')
buffer_.seek(0)
dataPIL = Image.open(buffer_)
data = np.asarray(dataPIL)
print(data)
buffer_.close()
```


```python
import matplotlib.pyplot as plt
import pylab
import numpy as np
from PIL import Image
from io import BytesIO

buffer_ = BytesIO()
for _ in range(10):
    a = np.arange(1,10,0.01)
    y = a ** 2
    figure = plt.figure()
    plt.plot(a,y)
    figure.savefig(buffer_,format = 'jpg')
buffer_.seek(0)
dataPIL = Image.open(buffer_)
data = np.asarray(dataPIL)
x = data.shape[0]
y = data.shape[1]
t = Image.new('RGB',(x,y))
for i in range(0,x):
    for j in range(0,y):
        t.putpixel([i,j],tuple(data[i][j]))
print(data.shape)
t.show()
buffer_.close()
```
