---
title: 信息熵到底是什么
date: 2018-06-09 18:06:16
categories:
- 数学
- 基础
tags:
- 信息熵
- basis
- math
---
[转载](https://blog.csdn.net/saltriver/article/details/53056816)

信息是我们一直在谈论的东西，但信息这个概念本身依然比较抽象。在百度百科中的定义：信息，泛指人类社会传播的一切内容，指音讯、消息、通信系统传输和处理的对象。

 <!-- more -->

但信息可不可以被量化，怎样量化？答案当然是有的，那就是“信息熵”。早在1948年，香农(Shannon)在他著名的《通信的数学原理》论文中指出：“信息是用来消除随机不确定性的东西”，并提出了“信息熵”的概念（借用了热力学中熵的概念），来解决信息的度量问题。 

香农 

下面结合自身的一些认识，谈谈对信息熵的理解。

## 信息熵是消除不确定性所需信息量的度量，也即未知事件可能含有的信息量。

一个事件或一个系统，准确的说是一个随机变量，它有着一定的不确定性。例如，“除东道主俄罗斯外，哪31个国家能进军2018年俄罗斯世界杯决赛圈”，这个随机变量的不确定性很高，要消除这个不确定性，就需要引入很多的信息，这些很多信息的度量就用“信息熵”表达。需要引入消除不确定性的信息量越多，则信息熵越高，反之则越低。例如“中国男足进军2018年俄罗斯世界杯决赛圈”，这个因为确定性很高，几乎不需要引入信息，因此信息熵很低。 

俄罗斯世界杯 

那信息熵如何计算呢？举个吴军在《数学之美》中一样的例子，假设世界杯决赛圈32强已经产生，那么随机变量“2018年俄罗斯世界杯足球赛32强中，谁是世界杯冠军？”的信息量是多少呢？ 

数学之美 

根据香农(Shannon)给出的信息熵公式，对于任意一个随机变量X，它的信息熵定义如下，单位为比特(bit)：

H(X)=−∑xεX [P(x)logP(x)] (log 是以 2 为底，要注意前面的负号)

那么上述随机变量（谁获得冠军）的信息量是：

H=-(p1·logp1+p2·logp2+…p32·logp32)

其中，p1,p2,…,p32分别是这32强球队夺冠的概率。 

吴军的书中给出了几个结论：一是32强球队夺冠概率相同时，H=5；二是夺冠概率不同时，H<5；三是H不可能大于5。

对于第一个结论：结果是很显然的，夺冠概率相同，即每个球队夺冠概率都是1/32，所以H=-((1/32)·log(1/32)+(1/32)·log(1/32)+…+(1/32)·log(1/32))=-log(1/32)=log(32)=5(bit)

对于第二个结论和第三个结论：使用拉格朗日乘子法进行证明，详见《求约束条件下极值的拉格朗日乘子法》。这实际上是说系统中各种随机性的概率越均等，信息熵越大，反之越小。

从香农给出的数学公式上可以看出，信息熵其实是一个随机变量信息量的数学期望。

## 日常语境中的信息量与信息熵的关系。

日常生活中，我们经常说某人说话言简意赅，信息量却很大，某些人口若悬河，但是废话连篇，没啥信息量；这个电视剧情节太拖沓，一集都快演完了也没演啥内容。这里的信息量/内容与信息熵有什么关系呢？

很多人把这些东西与信息熵混为一谈，得出“说话信息量越大，信息熵越高”“语言越言简意赅，信息熵越高；语言越冗余堆积，信息熵越低。”等等结论。

不是说这些说法错了，而是容易引起误导。个人认为，这里日常语境的信息量与其说是信息量，不如说是信息质量和信息传递效率问题，有没有干货，有没有观点，有没有思想，并且在一定的文字长度/播放时间内，能不能有效的表达出来，这个其实是人的能力问题，和信息熵没啥关系好不！

## 在自然语言处理中，信息熵只反映内容的随机性（不确定性）和编码情况，与内容本身无关。

信息熵的一个重要应用领域就是自然语言处理。例如，一本50万字的中文书平均有多少信息量。我们知道，常用的汉字约7000字。假如每个汉字等概率，那么大约需要约13比特（即13位二进制数，213=8192）表示一个汉字。 

应用信息熵就是，一个汉字有7000种可能性，每个可能性等概率，所以一个汉字的信息熵是： 

H=-((1/7000)·log(1/7000)+(1/7000)·log(1/7000)+…(1/7000)·log(1/7000))=12.77(bit)

实际上由于前10%汉字占常用文本的95%以上，再考虑词语等上下文，每个汉字的信息熵大约是5比特左右。所以一本50万字的中文书，信息量大约是250万比特。需要注意这里的250万比特是个平均数。 

再看下面两个句子。 

（1）落霞与孤鹜齐飞，秋水共长天一色。 
（2）落日下的晚霞与孤独的大雁一同飞翔，晚秋的江水和深远的天空连成一片。 

落霞 

按照信息熵的计算，第二句比第一句的信息熵要高1倍以上，你会觉得第二句比第一句水平要高，信息量更大么？在自然语言处理中出现较大的信息熵，只表示可能出现的语言字符较多，并不意味着你可以从中得到更多的信息。

所以，信息熵高，不代表你说的话，写的文字中蕴含的信息量就比别人高了。更确切的，信息熵在自然语言处理中是用来对语言文字进行数据压缩的，与语言素养无关。当然条件熵、相对熵等应该是自然语言处理中更有用的概念，这个以后有机会再说。

## 随机变量的信息熵大小是客观的，又是主观的，与观测者的观测粒度有关。

信息熵描述的是随机变量的不确定性。对于同一个随机变量，不同观测者从不同粒度上观察，得到的信息熵是不一样的。

还是举上面世界杯谁夺得冠军的例子，32强谁夺得冠军的信息熵是5比特；如果粒度再粗些，有人关注是哪个州夺得冠军，那么其可能性是5种（欧洲，南美，非洲，中北美州，亚洲），信息熵是2.32比特；如果我们只关注亚洲是否夺冠，那么可能性是2种，信息熵是1比特。 

股市

再举个更随机的例子，中国股市的涨跌（假设非涨即跌，不算平盘），明天是涨还是跌，只有2种可能，信息量（信息熵）是1比特；假设股市只有蓝筹板，中小板，创业板3个板块，这三个板块的涨跌的可能性合计是8种，信息熵是3比特；如果关注的是每个股票的涨跌，2000个股票的可能性合计是22000种，信息熵是2000比特。

因此，对于不同的观测者来说，信息量(信息熵)是不同的，观测粒度越细则信息量(信息熵)越大，观测粒度越粗则信息量(信息熵)越小。

## 信息熵与热力学熵有相似之处，但不是同一个东西。

很多人看到信息熵的数学公式和热力学熵（有宏观形式和微观形式2种）的数学表达式非常相似，觉得两者不存在本质区别。但个人认为至少在目前看来是完全不一样的东西。

首先，两者的应用领域完全不一样。热力学熵主要用于热力学、量子物理等领域。而信息熵主要用于通信和和计算机等领域。

其次，两者形成于不同的理论体系，含义、研究对象都有较大区别，连量纲都不一样，一个是焦耳/开尔文,一个是比特bit。举个可能不太恰当的例子，计算机科学中借用了植物学中“树”的概念，虽然计算机科学中的“树”与植物学中的“树”很多地方类似，但显然不是同一个东西。 

植物树二叉树

最后，不排除将来有一套统一理论体系能够将信息熵和热力学熵都包含进去。但在现在看来，信息论和热力学并没有统一的迹象，生搬硬套的将这两种熵联系在一起，只会起到概念含混，逻辑脆弱的反作用。

## 举一个简单的代码例子：

```python
import math
def calcShannon(dataSet): #计算香农熵（信息熵）
    num = len(dataSet)
    labels = {}
    for feat in dataSet:
        currentLabel = feat[-1] #得到标签
        if currentLabel not in labels.keys():
            labels[currentLabel] = 0
        labels[currentLabel] += 1
    shannon = 0.0
    for key in labels:
        prob = float(labels[key]) / num
        shannon -= prob * (math.log(prob,2))
    return shannon

def createData():
    dataSet = [
        [1,1,'yes'],
        [1,1,'yes'],
        [1,0,'no'],
        [0,1,'no'],
        [0,1,'no']
    ]
    return dataSet

dataSet = createData()
print(calcShannon(dataSet))
    # 0.9709505944546686


dataSet[0][-1] = 'maybe'
print(calcShannon(dataSet))
    # 1.3709505944546687

#可以看出 信息熵的大小主要和数据的复杂度有关
```
