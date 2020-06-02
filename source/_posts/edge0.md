---
title: 开源项目 | edge-connect
date: 2020-06-02 22:01:10
categories:
- [开源项目,深度学习,edge-connect]
tags:
- edge-connect
---
这是一个开源项目，我已经成功在集群上部署，并且已经跑了一遍。

整个项目可以有两个效果

- 修复图片的缺失
- 去掉不想要的元素

效果还是蛮酷炫的。

<!-- more -->

<br/>

# 参考内容

<br/>

- [项目代码](https://github.com/knazeri/edge-connect#getting-started)
- [论文地址](https://arxiv.org/abs/1901.00212)

<br/>

# 安装过程

<br/>

## 安装背景

- ubuntu16.04
- anaconda

## 创建虚拟环境

	conda create -n edge python=3.5

并且进入虚拟环境

## clone 代码

选择自己喜欢的文件夹

	git clone https://github.com/knazeri/edge-connect.git
	cd edge-connect

## 安装 torch

	pip install torch==1.0.0 -i https://pypi.tuna.tsinghua.edu.cn/simple
	pip install torchvision==0.2.1 -i https://pypi.tuna.tsinghua.edu.cn/simple

## 安装其它依赖

	pip install -r requirements.txt

## 下载官方的 checkpoints

- [checkponits/r8v4](https://pan.baidu.com/s/15RuE-Vde_R574DDH2LU62w)

假设，你的项目地址是

	/data/aaa/edge-connect

然后移动 `checkpoint`

	cd /data/aaa/edge-connect
	mkdir checkpoints
	mv path_of_master.zip /data/aaa/edge-connect/checkpoints

这个 `master.zip` 就是上面打包的 `checkpoints` 压缩包。

	cd /data/aaa/edge-connect/checkpoints
	unzip master.zip
	这个时候可以 ls 看一下

这个时候，基本的配置都已经做好了。

<br/>

# 运行

<br/>

```python
python test.py \
  --model [stage] \
  --checkpoints [path to checkpoints] \
  --input [path to input directory or file] \
  --mask [path to masks directory or mask file] \
  --output [path to the output directory]
```

这个要解释一下

- `model` :这个 `model` 默认是 3，其他的我没有试过，可以自己读源码进行尝试
- `checkpoints` 这个是官方已经训练好的参数集，一共有 3 个，我猜测，可能和 `model` 一一对应，所以，这点要注意
- `input` 这个是要处理的图片文件夹
- `mask` 这个是处理图片中每一个图片使用的遮罩
- `output` 这个是要输出的结果图片的文件夹

## 图片和遮罩

我们是预处理图片和遮罩一一对应。

这里面的一一对应有两个含义

- 名字相同，预处理图片和遮罩图片名字相同
- 遮罩路径和预处理图片中的路径相同
- 对应图片长宽必须相等

举一个例子，假设我们的预处理图片放在叫做

	/data/aaa/edge-connect/example/places/images

里面，并且叫做

	places2_01.png

那么，同样在

	/data/aaa/edge-connect/example/places/masks

里面需要有一个遮罩叫做

	places2_01.png

上面的路径是我胡乱编的，但是，其核心点在于名字要相同。

那我们来看看这两个文件究竟有什么相似之处。

### 预处理图片

- RBG 模式
- 最好是 256 * 256
- 通道显示为 （w,h,3）

![](/images/edge/0_0.png)

### 遮罩图片

- gray 模式
- 最好是 256 * 256
- alpha 通道：否

![](/images/edge/0_1.png)

这里要着重强调，预测图和遮罩图并不一定是 256 * 256 ，但是，必须长宽相等。

可以看出其路径是一样的，要特别注意，路径必须相同，否则会出现不可预知的情况。

这里，我将提供一个程序，是将遮罩和原图片进行混合的。

```python
import cv2
import numpy as np

a = cv2.imread('./places2_02.png')
b = cv2.imread('./places2_01.png')

cv2.imwrite('./test.png', np.bitwise_or(b, a))
```

这里面 `places2_01.png` 代指遮罩，也就是

![](/images/edge/0_1.png)

`/places2_02.png)` 是指原图片，也就是

![](/images/edge/0_2.png)

经过合成，就形成了

![](/images/edge/0_0.png)

## 运行代码

举一个简单的运行代码的例子

```python
python test.py \
  --checkpoints /data/aaa/edge-connect/checkpoints/places2  \
  --input /data/aaa/edge-connect/examples/places2/images \
  --mask /data/aaa/edge-connect/examples/places2/masks \
  --output /data/aaa/edge-connect/checkpoints/resluts
```

其中，`output` 的文件夹不需要自己建。

最后运行会非常流畅。

<br/>

# 其他

<br/>

## 出现异常

或许和你图片的权限有关，你可以使用

	ls -lah

看看你是否有这些图片的权限，如果没有，可以进入管理员权限，运行

	chmod 777 文件名