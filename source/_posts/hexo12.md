---
title: hexo | 某些小修饰（加粗，颜色变化等）
date: 2020-01-27 19:57:57
categories:
- hexo
tags:
- hexo
---
我们经常在撰写文章的时候需要对文字进行一些修饰。

比如，有的地方进行变颜色，加粗，斜体等，那么在 hexo 中如何做到这些？

<!-- more -->

其实，答案很简单，使用 html 的知识就好了。

<br/>

# 加粗

<br/>

	<b>粗体</b>

<b>粗体</b>

<br/>

# 变颜色

<br/>

	<p style="color: #FF0000;">红色</p>

<p style="color: #FF0000;">红色</p>

下面是搜集的颜色列表：

![](/images/hexo/12_0.png)

<br/>

# 斜体

<br/>

	<i>斜体</i>

<i>斜体</i>

<br/>

# 删除体

<br/>

	<del>删除体</del>

<del>删除体</del>

<br/>

# 插入字

<br/>

	<ins>插入字</ins>

<ins>插入字</ins>

<br/>

# 上标字

<br/>
	
	<sup>上标字</sup>123

<sup>上标字</sup>123

<br/>

# 下标字

<br/>
	
	123<sub>下标字</sub>

123<sub>下标字</sub>

<br/>

# 标签

<br/>

## 数字标签

1. 这是一个标签

2. 这是第二个标签

上面的标签用法是：

	1. 这是一个标签

	2. 这是第二个标签
		数字 + . + 空格

## 符号标签

<li style="margin-left: 40px;font-size: 15px">123</li>

上面的使用这个实现的

	<li style="margin-left: 40px;font-size: 15px">123</li>

我们的 li 标签要放在一行中，不然，每一个 li 标签，会被添加 <br/>

<li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li>

	<li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li><li style="margin-left: 40px;font-size: 15px">123</li>

如果不放在一行会出现

<li style="margin-left: 40px;font-size: 15px">123</li>
<li style="margin-left: 40px;font-size: 15px">123</li>
<li style="margin-left: 40px;font-size: 15px">123</li>

很难看

<br/>

# 好看的排列

<br/>

当我们只用 # 这个标签的时候，最好添加 <br/>

	<br/>
	
	# ***
	
	<br/>

这样整体排列很好看。

## 图形标签

一个非常偶然的机会发现官方的图形标签。

- 1
    - 1
        - 1
            - 1
- 2
- 3

其上面的表示方式如下：

	- 1
	    - 1
	        - 1
	            - 1
	- 2
	- 3

<br/>

# 表格

<br/>

|表格|属性|样式|
|---|---|---|
|一|二|三|

其代码如下

	|表格|属性|样式|
	|---|---|---|
	|一|二|三|

假设有一个单元格的内容太多了，想要换行，可以

|表格|属性|样式|
|---|---|---|
|一|二|我们过了江，进了车站。我买票，他忙着照看行李。行李太多，得向脚夫11行些小费才可过去。<br/>他便又忙着和他们讲价钱。我那时真是聪明过分，总觉他说话不大漂亮，非自己插嘴不可，但他终于讲定了价钱；|

代码如下

	|表格|属性|样式|
	|---|---|---|
	|一|二|我们过了江，进了车站。我买票，他忙着照看行李。行李太多，得向脚夫11行些小费才可过去。<br/>他便又忙着和他们讲价钱。我那时真是聪明过分，总觉他说话不大漂亮，非自己插嘴不可，但他终于讲定了价钱；|

但是如果想要合并单元格，就需要借助 html 元件了。

<table>
    <tr>
    	<td>最初价格</td>
        <td>买卖价格</td>
        <td>合约方向</td>
        <td>交割价格</td>
        <td>计算公式</td>
        <td>盈亏情况</td>
    </tr>
    <tr>
    	<td rowspan="4">1000U</td>
        <td rowspan="2">800U</td>
        <td rowspan="2">看空</td>
        <td>1200U</td>
        <td rowspan="2">合约张数 * 合约乘数 * （1/平仓价格 - 1/开仓价格）</td>
        <td>1 * 1 * (1/1200 - 1/800) =<br/> -(1/2400)BTC</td>
    </tr>
    <tr>
        <td>700U</td>
        <td>1 * 1 * (1/700 - 1/800) =<br/> 1/5600 BTC</td>
    </tr>
    <tr>
        <td rowspan="2">1200U</td>
        <td rowspan="2">看涨</td>
        <td>1400U</td>
        <td rowspan="2">合约张数 * 合约乘数 * （1/开仓价格 - 1/平仓价格）</td>
        <td>1 * 1 * (1/1200 - 1/1400) =<br/> 1/9800 BTC</td>
    </tr>
    <tr>
        <td>800U</td>
        <td>1 * 1 * (1/1400 - 1/1200) =<br/> -1/6400 BTC</td>
    </tr>
</table>

代码如下

```html
<table>
    <tr>
    	<td>最初价格</td>
        <td>买卖价格</td>
        <td>合约方向</td>
        <td>交割价格</td>
        <td>计算公式</td>
        <td>盈亏情况</td>
    </tr>
    <tr>
    	<td rowspan="4">1000U</td>
        <td rowspan="2">800U</td>
        <td rowspan="2">看空</td>
        <td>1200U</td>
        <td rowspan="2">合约张数 * 合约乘数 * （1/平仓价格 - 1/开仓价格）</td>
        <td>1 * 1 * (1/1200 - 1/800) =<br/> -(1/2400)BTC</td>
    </tr>
    <tr>
        <td>700U</td>
        <td>1 * 1 * (1/700 - 1/800) =<br/> 1/5600 BTC</td>
    </tr>
    <tr>
        <td rowspan="2">1200U</td>
        <td rowspan="2">看涨</td>
        <td>1400U</td>
        <td rowspan="2">合约张数 * 合约乘数 * （1/开仓价格 - 1/平仓价格）</td>
        <td>1 * 1 * (1/1200 - 1/1400) =<br/> 1/9800 BTC</td>
    </tr>
    <tr>
        <td>800U</td>
        <td>1 * 1 * (1/1400 - 1/1200) =<br/> -1/6400 BTC</td>
    </tr>
</table>
```

<br/>

# 修改图片大小

<br/>

    <div style="width: 50%;padding-left: 25%">
    ![](/images/software/5_0.jpg)
    </div>

另外剧中的话使用 text-align:center; 不管用，需要使用上面的代码。