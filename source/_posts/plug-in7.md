---
title: 群控模拟器
date: 2019-03-11 18:54:29
categories:
- plug_in
tags:
- 群控
- 多线程
- xposed
- XPosed
- airtest
- popc
- 手机
- 手机模拟器
- android
- 模拟器
- multiprocessing
- 外挂
- 手机外挂
---

这个效果很好,而且运用的都是基础 python 库,代码逻辑也非常简单.

<!-- more -->

## 参考资料

[python手机群控 adb命令 实现](https://blog.csdn.net/m0_38124502/article/details/79912539)

[python appium群控雷电模拟器](https://zhuanlan.zhihu.com/p/51223874)

## 我的解决方案

解决方案主要是利用多进程,也就是 multiprocessing 库.

具体的代码如下

```python
# -*- encoding=utf8 -*-
import time
__author__ = "Administrator"
import os
import random
from airtest.core.api import *
import multiprocessing as mp

# auto_setup(__file__)

from poco.drivers.android.uiautomation import AndroidUiautomationPoco

def test(port):
    device_1 = connect_device('android:///127.0.0.1:' + str(port) + '?cap_method=javacap&touch_method=adb')
    poco = AndroidUiautomationPoco(device_1,use_airtest_input=True, screenshot_each_action=False)
    poco(text="网易云音乐").click()
    time.sleep(2)
    poco.swipe([0.5,0.8],[0.5,0.2])

if __name__ == '__main__':
    p1 = mp.Process(target=test,args=(62029,))
    p2 = mp.Process(target=test, args=(62026,))
    p3 = mp.Process(target=test, args=(62027,))
    p4 = mp.Process(target=test, args=(62028,))
    p5 = mp.Process(target=test, args=(62001,))
    p1.start()
    p2.start()
    p3.start()
    p4.start()
    p5.start()
    p1.join()
    p2.join()
    p3.join()
    p4.join()
    p5.join()
```
这里需要注意的是多进程的 start 和 join 的顺序一定要是这样,不然效果就是单核.
到后期,我会提供更加健壮的代码.






























