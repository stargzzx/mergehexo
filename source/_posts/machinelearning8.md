---
title: 预测数值型数据：回归
date: 2018-06-14 16:56:34
categories:
- machine learning
tags:
- 回归
- machine learning
---

[github](https://github.com/benpaodewoniu/Machine-Learning-in-Action)

说道回归，我们一般指的是线性回归，线性回归的一般理论是：

<!-- more -->

给你一个输入值，然后有一个计算公式，从而由输入值转变为输出值。

在看这个算法之前，你需要了解最小二乘法，可以参考我以前的博文。

[最小二乘法](https://benpaodewoniu.github.io/2018/06/17/math4/)

<br/>

# 理论

<br/>

## 介绍

线性回归理论认为，输入项分别乘以一些常量，再将结果加起来，就能得到输出值，比如：
 
	housePrice = area * 0.1 + years * 0.02 + 。。。
	
但是也有非线性回归理论，他们的认为输入值之间都是有联系的：

	housePrice = ( area / years ) * 0.1
	
但是，我们探究的皆是线性回归。

## 原理

假设所有的常量都在 W 向量中，所有的每一个输入项都在 Xi 向量中(N * 1)，则对于一个样例来说：

	#octave语法
	Y1 = X1' * W
	
但是，算法的关键就是如何计算出 W 向量。

事实上，我们一开始对 W 向量都是随机初始化，当然初始化的方案有很多。

但是随机初始化的结果肯定不是最终的 W ，所以，在这里我们引入误差，最常用的是平方误差。

再次再次重申一下各符号的意义 Y 是存储标签的向量 X 是存储输入项的矩阵（它的每一行代表一个样例） W 是常量的向量 J 是误差值。

这里有一个注意点，首先 X 是存储所有输入项的矩阵，但是 Xi 是存储单例的向量，其形式是 N * 1。

	#octave语法
	J = ∑ (Y- Xi' * W)^2 [Xi 是一个向量 ，i = 1 ... ... N]
	#用矩阵表示
	J = (Y - X * W)' * (Y - X * W)
	
因为 J 是一个平方函数，所以自然而然我们就能想到，其图像是一个碗形，那么最优点必然是 J 最小的点，也就是碗底，那么只要用办法接近碗底那么我们就能计算出 w。

一般，计算 w 我们有两种方法，一是直接求（直接求不需要借助平方误差），二是用梯度下降。

### 直接求 W

考虑下面的线性代数：

	#octave语法 pinv(A)是求逆
	Y = X * W
	pinv(X) * Y = pinv(X) * X * W
	pinv(X) * Y = W
	pinv(X) * pinv(X') * X' * Y = W
	pinv(X' * X) * X' * Y = W
	
在书中的最终式子如图所示：

![](/images/machinelearningalgorithm/2_0.JPG)

看到上面的式子，你大概有两个疑问。

1. 为什么会有导数第二段那么多余的式子？

因为正确的推导公式是求导求出来的，不过，我对那个推导过程很有疑虑，所以最后只能用验证的方法求出书中的式子。

2. 为什么不直接 W = pinv(X) * Y 还非得多加一个，成为 pinv(X' * X) * X' * Y = W？

如图：

![](/images/machinelearningalgorithm/2_1.JPG)

因为本来按照求导得出来的式子，正好和第一个图的式子一样，至于为什么不化成更简单的式子，其实很简单。

因为我们不知道下面图片是否有逆。

![](/images/machinelearningalgorithm/2_2.JPG)

假设，X 是 m * n 其中 m < n

	R（X） <= m
	而图片中的式子，其形式 = n * n
	又因为上面式子的秩必然 <= m
	所以这种情况没有逆

以上就是直接求的原理。其最终式子，我在这里再贴一下：

![](/images/machinelearningalgorithm/2_0.JPG)

### 用梯度下降的方式求 W

<br/>

# 代码示例

<br/>

## 直接求 W

### 代码编辑

首先你可以在我的github中看见相关的数据。我用的是 ex0.txt。

我用 octave 画出 其中的数据分布，如图：

![](/images/machinelearningalgorithm/2_3.jpg)

而我们的目的就是拟合出一条线，使得误差 J 最小，我们决定使用直接求的方法。参见下述代码：

{% codeblock %}
import numpy as np

def loadData(filename):
    numFeat = len(open(filename).readline().split('\t')) - 1
    dataMat = []
    labelMat = []
    fr = open(filename)
    for line in fr.readlines():
        lineArr = []
        curLine = line.strip().split('\t')
        for i in range(numFeat):
            lineArr.append(float(curLine[i]))
        dataMat.append(lineArr)
        labelMat.append(float(curLine[-1]))
    return dataMat,labelMat

def standRegression(x,y):
    xMat = np.mat(x)
    yMat = np.mat(y).T
    xTx = xMat.T * xMat
    if np.linalg.det(xTx) == 0.0:
        print('不可逆')
        return
    ws = xTx.I * (xMat.T * yMat)
    return ws

x,y = loadData('ex0.txt')
print(standRegression(x,y))
    #[[3.00774324]
    # [1.69532264]]
{% endcodeblock %}

根据上得到的 W，用 octave 绘图：

![](/images/machinelearningalgorithm/2_4.jpg)

### 注意点

我们再次观察 ex0.txt 中的数据：

	1.000000	0.067732	3.176513
	
第一个是 1，第二个是输入项，第三个是标签，那么为什么第一个数值总是 1 。其实， 1 代表的是偏差。

我们都知道直线的方程如下形式：

	y = k * x + b

那个 b 就是偏差。所以，我们的第一个数值为 1 ，就是要求偏差 b。

<br/>

# 欠拟合

<br/>

在这里我不在讲述欠拟合的定义，关于欠拟合的解决方案，这里是局部加权线性回归方法(LWLR)。

## LWLR

### 原理

在该算法中，我们给待测点附近的每一点赋予一定的权重。什么叫赋予权重呢？我们考虑之前的计算方式，最终 W 的计算数值是根据所有样本得到的，讲究的是一个大致的平均，最终得到的是一条直线。

但是，我们知道对于单一样本来说离它近的点和它相似，离它远的点和它不同，所以对于单一样本点如果采用全部样本对它进行归纳的话不免会失去它的特征，但是如果对某一单一样本之外的点赋予权重，比如离它近的权重大，离它远的权重小，那么，最后就可以对这个单一样本点得到独立的 Wi。

最终，每一个样本点都有一个相应的 Wi ，所以最后的表现形式是曲线。

在这里要说明的是，当所有样本点对单一样本贡献一样的时候，最后会是一条直线，也就是和原来一样，产生欠拟合的效果。

当样本对单一样本的权重不一样的时候，离得越近权重越大，是最合适的情况。

但是，当只有最近的样本才对权重有贡献，甚至极端一点，只有自身才对自己有贡献的时候，会产生过拟合的效果。

那我们应当如何选权重呢？

首先给出计算 W 的新式子：

![](/images/machinelearningalgorithm/2_5.JPG)

而上面式子中的矩阵 A 就是用来给每一个数据赋予权重。

LWLR使用核概念（和支持向量机的核概念一样）。核有很多，最常见的就是高斯核，高斯核对应的权重如下：

![](/images/machinelearningalgorithm/2_6.JPG)

观看如下图片：

![](/images/machinelearningalgorithm/2_7.jpg)

假设我们要观测的点是1.5，这就是不同的 K 的取值，其大约有多少样本点对样本点 1.5 的贡献。可以看出 K 越小，越少的样本点才有权重。

当 K = 1 的时候，所有的样本点对单一样本的贡献权重一样，会出现欠拟合状态，如果 K 的值非常小，那么会出现过拟合的状态。

### 看代码解释

{% codeblock %}  
import numpy as np
import operator

def loadData(filename):
    numFeat = len(open(filename).readline().split('\t')) - 1
    dataMat = []
    labelMat = []
    fr = open(filename)
    for line in fr.readlines():
        lineArr = []
        curLine = line.strip().split('\t')
        for i in range(numFeat):
            lineArr.append(float(curLine[i]))
        dataMat.append(lineArr)
        labelMat.append(float(curLine[-1]))
    return dataMat,labelMat

def lwlr(testPoint,xArr,yArr,k):
    xMat = np.mat(xArr)
    yMat = np.mat(yArr).T
    m    = np.shape(xMat)[0]
    weighs = np.mat(np.eye(m))
    for j in range(m):
        diffMat = testPoint - xMat[j,:]
        weighs[j,j] = np.exp(diffMat * diffMat.T/(-2.0 * k ** 2))
    xTx = xMat.T * (weighs * xMat)
    if np.linalg.det(xTx) == 0.0:
        return
    ws = xTx.I * (xMat.T * (weighs * yMat))
    return testPoint * ws                          #返回的是预测的 y 值

def lwlrTest(testArr,xArr,yArr,k):
    m = np.shape(testArr)[0]
    yHat = np.zeros(m)
    y = {}
    for i in range(m):
        yHat[i] = lwlr(testArr[i],xArr,yArr,k)
        y[yHat[i]] = testArr[i][1]
    y = sorted(y.items(), key=operator.itemgetter(1), reverse=False)
    return yHat,y

data,label = loadData('ex0.txt')
yHat,y = lwlrTest(data,data,label,0.003)
file = open('test.txt','w')
for f in y:
    file.writelines(str(f[0]) + '\t' + str(f[1]))
    file.write('\n')
file.close()
{% endcodeblock %}

### 图像

![](/images/machinelearningalgorithm/2_8.jpg)

我们从图上也可以清晰的看出当 k  = 0.01 的时候拟合程度是最好的。

在 github 中我还放置了预测鲍鱼年龄的数据（abalone.txt），有兴趣的可以去玩一下。

<br/>

# 缩减系数来理解数据

<br/>

## 什么是缩减系数？

通过对损失函数(即优化目标)加入惩罚项，使得训练求解参数过程中会考虑到系数的大小，通过设置缩减系数(惩罚系数)，会使得影响较小的特征的系数衰减到0，只保留重要的特征。常用的缩减系数方法有lasso(L1正则化)，岭回归(L2正则化)。

什么是缩减？

	通过引入 +λI 来限制所有 W 之和，通过引入惩罚项，能够减少不重要的参数，这个技术在统计学中叫做缩减。

## 缩减系数的目的

### 消除噪声特征:

	如果模型考虑了一些不必要的特征，那么这些特征就算是噪声。噪声是没必要的，使得模型复杂，降低模型准确性，需要剔除。

### 消除关联的特征

	如果模型的特征空间中存在关联的特征，这会使得模型不适定，即模型参数会有多解。训练得到的只是其中一个解，这个解往往不能反映模型的真实情况，会误导模型的分析与理解。训练求解的模型参数受样本影响特别大，样本变化一点点，参数解就跳到另一组解去了。总之，模型是不稳定的。
	

## 岭回归（ridge regression）

在上述的 LWLR 中，我们很容易知道这个算法的缺点，它对每一个单独样本计算权重的时候，都需要整合所有的数据，假设数据量非常大，那么这个算法的时间就会比较长。

而且，我们知道， LWLR 的前提是下面的式子逆矩阵存在：

![](/images/machinelearningalgorithm/2_9.JPG)

我们考虑这样一种情况，就是我们的特征比样本还多，也就是说输入矩阵 X 不是满秩矩阵（奇异矩阵）。

满秩矩阵

	设A是n阶矩阵, 若r（A） = n, 则称A为满秩矩阵。但满秩不局限于n阶矩阵。
	若矩阵秩等于行数，称为行满秩；若矩阵秩等于列数，称为列满秩。
	既是行满秩又是列满秩则为n阶矩阵即n阶方阵。
	行满秩矩阵就是行向量线性无关，列满秩矩阵就是列向量线性无关；所以如果是方阵,行满秩矩阵与列满秩矩阵是等价的。

矩阵的定义

	由 m × n 个数a排成的m行n列的数表称为m行n列的矩阵，简称m × n矩阵。

X 不是满秩矩阵，在计算下面式子的时候就会出错。

![](/images/machinelearningalgorithm/2_9.JPG)

而为了让上述式子成立，统计学家引入岭回归的概念。

岭回归就是在矩阵 X'X 上加一个 λI 从而使矩阵变成非奇异矩阵。进而对 X'X + λI 求逆。

所以回归公式变成：

![](/images/machinelearningalgorithm/2_10.JPG)

上面式子 λ 是一个常量，当 λ 等于 0 的时候，原式就退变成为最小二乘法， I 是单位矩阵。为什么说为岭呢？正是因为 I 是单位矩阵，其对角线有数，其它的为零，所以像山岭。

岭回归是一种专用于共线性数据分析的有偏估计回归方法，实质上是一种改良的最小二乘估计法，通过放弃最小二乘法的无偏性，以损失部分信息、降低精度为代价获得回归系数更为符合实际、更可靠的回归方法，对病态数据的拟合要强于最小二乘法。

对于有些矩阵，矩阵中某个元素的一个很小的变动，会引起最后计算结果误差很大，这种矩阵称为“病态矩阵”。有些时候不正确的计算方法也会使一个正常的矩阵在运算中表现出病态。对于高斯消去法来说，如果主元（即对角线上的元素）上的元素很小，在计算时就会表现出病态的特征。

再加上 λI 后，其变成奇异矩阵的概率下降。

### 代码示例

{% codeblock %}
import numpy as np
def loadData(filename):
    numFeat = len(open(filename).readline().split('\t')) - 1
    dataMat = []
    labelMat = []
    fr = open(filename)
    for line in fr.readlines():
        lineArr = []
        curLine = line.strip().split('\t')
        for i in range(numFeat):
            lineArr.append(float(curLine[i]))
        dataMat.append(lineArr)
        labelMat.append(float(curLine[-1]))
    return dataMat,labelMat

def ridgeRegress(xMat,yMat,lam):
    xTx = xMat.T * xMat
    denom = xTx + np.eye(np.shape(xMat)[1]) * lam
    if np.linalg.det(denom) == 0.0:
        return
    ws = denom.I * (xMat.T * yMat)
    return ws

def ridgeTest(xArr,yArr):
    xMat = np.mat(xArr)
    yMat = np.mat(yArr).T
    yMean = np.mean(yMat,0)
    yMat = yMat - yMean
    xMean = np.mean(xMat,0)
    xVar = np.var(xMat,0)
    numTest = 30
    wMat = np.zeros((numTest,np.shape(xMat)[1]))
    for i in range(numTest):
        ws = ridgeRegress(xMat,yMat,np.exp(i - 10))
        wMat[i,:] = ws.T
    return wMat

abX,abY = loadData('abalone.txt')
rideg = ridgeTest(abX,abY)
{% endcodeblock %}

使用岭回归和缩减技术需要对特征进行标准化处理。

在上面代码的 ridgeTest() 中有数据归一化的应用，关于数据归一化，你可以参考我的博文。

[机器学习中的数据归一](https://benpaodewoniu.github.io/2018/06/17/machinelearning1/)

最后画出的图如下：

![](/images/machinelearningalgorithm/2_11.jpg)

abalone.txt 中的一个样例
	
	1	0.455	0.365	0.095	0.514	0.2245	0.101	0.15	15

为八个特征。

关于图像的解读，最后那个 ridge 是 30 * 8，行数分别代表不同的 lam ，列数代表每一个 lam 对应的 Wi。

每一个平行于 Y 轴的直线上的焦点对应于一个 Wi。

但是为什么只有八条曲线，是因为我们按照列的方式来计算的。横坐标代表 log(λ) 纵坐标代表那个特定的 λ 对应的 W。

从图上我们可知，假如 λ 非常小的时候，图像左边，那么 + λI 相当于不加，所以最后得到的 Wi 相当于普通回归一样。

假如 λ 非常大，图像右边，那个 + λI 确实会使得原来的特征权重降低，具体原理可以参考正则化去过拟合原理，最终导致 Wi 缩减为零。

所以，最好的 Wi 是取中间某一条直线上的 Wi 数值。但是具体取哪个需要用测试数据来敲定。

## 前向逐步回归

说实话，当我看见《机器学习实战》中的前向逐步回归算法感到很吃惊，哇塞，这个算法也太简陋了吧。

后来想想也就理解了，前向逐步回归说的是一种思想，并不是什么算法，所以前向逐步回归可以用贪心算法也可以用梯度下降算法。

### 前向逐步回归——贪心算法

前向逐步回归的原理是每一步进可能减少误差，一开始，所有的 W 都设为 1 。然后每一步的策略就是增加或减少 W 使得误差 J 减小。

所谓的贪心算法只是很笨的一种方法，如果真的要使用前向逐步回归，应该选用更好的算法。但是，在这里还是写一下贪心算法的代码吧。

{% codeblock %}
import numpy as np
import math
def loadData(filename):
    numFeat = len(open(filename).readline().split('\t')) - 1
    dataMat = []
    labelMat = []
    fr = open(filename)
    for line in fr.readlines():
        lineArr = []
        curLine = line.strip().split('\t')
        for i in range(numFeat):
            lineArr.append(float(curLine[i]))
        dataMat.append(lineArr)
        labelMat.append(float(curLine[-1]))
    return dataMat,labelMat


def rssError(yArr,yHatArr): #yArr and yHatArr both need to be arrays
    return ((yArr-yHatArr)**2).sum()

def regularize(xMat): # 数据归一化
    inMat = xMat.copy()
    inMeans = np.mean(inMat,0)
    inVar = np.var(inMat,0)
    inMat = (inMat - inMeans)/inVar
    return inMat

def greed(xArr,yArr,eps=0.01,num=100):
    xMat = np.mat(xArr)
    yMat = np.mat(yArr).T
    yMean = np.mean(yMat,0)
    xMat = regularize(xMat)
    m,n = np.shape(xMat)
    returnMat = np.zeros((num,n))
    ws = np.zeros((n,1))
    wsTest = ws.copy()
    wsMax = ws.copy()
    for i in range(num):
        print(ws.T)
        lowError = math.inf # 表示正无穷
        for j in range(n):
            for sign in [-1,1]:
                wsTest = ws.copy()
                wsTest[j] += eps * sign
                yTest = xMat * wsTest
                rssE = rssError(yMat.A,yTest.A)
                if rssE < lowError:
                    lowError = rssE
                    wsMax = wsTest
        ws = wsMax.copy()
        returnMat[i,:] = ws.T
    return returnMat

x,y = loadData('abalone.txt')
print(greed(x,y,0.01,200))
	#[[ 0.    0.    0.   ...  0.    0.    0.  ]
	# [ 0.    0.    0.   ...  0.    0.    0.  ]
	# [ 0.    0.    0.   ...  0.    0.    0.  ]
	# 。。。
	# [ 0.05  0.    0.09 ... -0.64  0.    0.36]
	# [ 0.04  0.    0.09 ... -0.64  0.    0.36]
	# [ 0.05  0.    0.09 ... -0.64  0.    0.36]]
	# 如果用最小二乘法计算，当然这个最小二乘法是直接求的最小二乘法，还有里面的数据也是和 greedy 一样进行了数据归一化处理。
	# 其最终输出如下：
	# [[ 0.0430442  -0.02274163  0.13214087  0.02075182  2.22403814 -0.99895312 -0.11725427  0.16622915]]
{% endcodeblock %}

在这里我可能对要产生的疑点进行解答。

	1.为什么 greedy 最后输出是不相等的数值？
	因为我们一共是三层循环，在第二层循环中我们是对每一个 Wi 进行赋值，所以最后不相等。
	2.为什么最终的 W1 和 W6 都是 0？
	这是因为这些特征对于贪心算法最后的所得的 W，可能是多余的。
	3.为什么最后几个的 W 都一样？
	这是因为对于最后的那些，因为步长太大的缘故，导致最后的误差进行震荡，而不能很好地缩减误差。

另外代码可以在我的 github 中找到。

下面贴一张当 lam = 0.005 迭代 1000 次的图像。

![](/images/machinelearningalgorithm/2_12.jpg)

前向逐步回归的主要作用

	这种算法的主要作用是可以找出重要的特征，这样就可以停止对不重要特征的搜集。
	就像上图中看到的，当迭代到一定次数后，发现有的特征最终趋向于零，所以我们可以放弃对该特征数据的搜集。

另外，我们应该要知道在应用缩减方法（岭回归和逐步线性回归）时，模型增加了偏差（使得有些特征被放弃），但是也减小了方差（误差  J）。



	

