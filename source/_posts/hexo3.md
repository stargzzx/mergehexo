---
title: 在 blog 中写数学公式
date: 2019-01-07 14:52:18
categories:
- hexo
tags:
- mathJax
- hexo
mathjax: true
---
转载文章。

[hexo中插入数学公式](http://stevenshi.me/2017/06/26/hexo-insert-formula/)

<!-- more -->

>原生hexo并不支持数学公式，需要安装插件 mathJax。mathJax 是一款运行于浏览器中的开源数学符号渲染引擎，使用 mathJax 可以方便的在浏览器中嵌入数学公式。mathJax 使用网络字体产生高质量的排版，因此可适应各种分辨率，它的显示是基于文本的而非图片，因此显示效果更好。这些公式可以被搜索引擎使用，因此公式里的符号一样可以被搜索引擎检索到。
下面是一个简单的 demo 。

When \\( a \ne 0 \\), there are two solutions to \\(ax^2 + bx + c = 0\\) and they are: 

$$ x = {-b \pm \sqrt{b^2-4ac} \over 2a} $$

让人值得庆幸的是，next 主题中已经自带了 mathJax ，所以，我们只需要激活就行了。

计入 next 主题，找到 _config.yml 文件。

改一下参数，如下：

	mathjax:
		enable: true
		per_page: true
		cdn: //cdn.bootcss.com/mathjax/2.7.1/latest.js?config=TeX-AMS-MML_HTMLorMML
		
虽然配置文件激活了，但是，页面还得激活，我们必须在开头声明一下才能使用 mathJax。这样可以节省资源，不需要每一个文件都加载 mathJax。

	---
	title: 在 blog 中写数学公式
	date: 2019-01-07 14:52:18
	categories:
	- hexo
	tags:
	- hexo
	mathjax: true
	---
	
这是这篇文章的声明。
## 符号

### 希腊符号

![](/images/hexo/3_0.png)

### 逻辑符号

![](/images/hexo/3_1.png)

### 集合运算符

![](/images/hexo/3_2.png)

### 箭头符号

![](/images/hexo/3_3.png)

### 关系运算符

![](/images/hexo/3_4.png)


### 其他运算符

![](/images/hexo/3_5.png)

下面来介绍一下语法。

## 语法

### 呈现位置

	$数学公式$ 行内 不独占一行
	$$数学公式$$ 行间 独占一行
	
{% codeblock %}
$f(x)=ax+b$
{% endcodeblock %}

显示效果为： $f(x)=ax+b$

如果是行间则使用：

{% codeblock %}
$$f(x)=ax+b$$
{% endcodeblock %}

$$f(x)=ax+b$$

### 语法格式

使用 ^ 表示上标，使用 _ 表示下标，如果上下标的内容多于一个字符，可以使用大括号括起来：

{% codeblock %}
$$f(x) = a_1x^n + a_2x^{n-1} + a_3x^{n-2}$$
{% endcodeblock %}

显示效果为：

$$f(x) = a_1x^n + a_2x^{n-1} + a_3x^{n-2}$$

如果左右两边都有上下标可以使用 \sideset 语法：

{% codeblock %}
$$\sideset{^n_k}{^x_y}a$$
{% endcodeblock %}

显示效果为：

$$\sideset{^n_k}{^x_y}a$$

### 正下方

如果是数学符号，那么直接用\limits命令放在正下方，如Max函数下面的取值范围，需要放在Max的正下方。可以如下实现：

{% codeblock %}
$$ \max \limits_{a<x<b}\{f(x)\} $$
{% endcodeblock %}

$$ \max \limits_{a<x<b}\{f(x)\} $$

若是普通符号，那么要用\mathop先转成数学符号再用\limits，如

{% codeblock %}
$$ \mathop{a}\limits_{i=1} $$
{% endcodeblock %}

$$ \mathop{a}\limits_{i=1} $$

### 括号

在 markdown 语法中，\, $, {, }, _都是有特殊含义的，所以需要加\转义。小括号与方括号可以使用原始的() [] 大括号需要转义\也可以使用\lbrace和 \rbrace

{% codeblock %}
\{x*y\}
\lbrace x*y \rbrace
{% endcodeblock %}

显示效果为：

$$\{x*y\}$$

原始符号不会随着公式大小自动缩放，需要使用 \left 和 \right 来实现自动缩放：

{% codeblock %}
$$\left \lbrace \sum_{i=0}^n i^3 = \frac{(n^2+n)(n+6)}{9} \right \rbrace$$
{% endcodeblock %}

$$\left \lbrace \sum_{i=0}^n i^3 = \frac{(n^2+n)(n+6)}{9} \right \rbrace$$

这一行显示不出来，没办法，我只能调到下面去了，式子是一样的

不使用\left 和 \right的效果：

{% codeblock %}
$$ \lbrace \sum_{i=0}^n i^3 = \frac{(n^2+n)(n+6)}{9}  \rbrace$$
{% endcodeblock %}

$$ \lbrace \sum_{i=0}^n i^3 = \frac{(n^2+n)(n+6)}{9}  \rbrace$$

鬼才知道上面那行为什么显示错误。。。

$$\left \lbrace \sum_{i=0}^n i^3 = \frac{(n^2+n)(n+6)}{9} \right \rbrace$$

### 分数与开方

可以使用\frac 或者 \over 实现分数的显示：

{% codeblock %}
$\frac xy$
$ x+3 \over y+5 $
{% endcodeblock %}

分别显示$\frac xy$和$ x+3 \over y+5 $

开方使用\sqrt:

{% codeblock %}
$ \sqrt{x^5} $
$ \sqrt[3]{\frac xy} $
{% endcodeblock %}

分别显示$ \sqrt{x^5} $和$ \sqrt[3]{\frac xy} $

### 求和与积分

求和使用\sum,可加上下标，积分使用\int可加上下限，双重积分用\iint:

{% codeblock %}
$ \sum_{i=0}^n $
$ \int_1^\infty $
$ \iint_1^\infty $
{% endcodeblock %}

分别显示：

$ \sum_{i=0}^n $和$ \int_1^\infty $和$ \iint_1^\infty $

### 极限

{% codeblock %}
$ \lim_{x \to 0} $
{% endcodeblock %}

显示为 $ \lim_{x \to 0} $

### 表格与矩阵

表格样式lcr表示居中，|加入一条竖线，\hline表示行间横线，列之间用&分隔，行之间用\分隔

{% codeblock %}
$$\begin{array}{c|lcr}
n & \text{Left} & \text{Center} & \text{Right} \\\\
\hline
1 & 1.97 & 5 & 12 \\\\
2 & -11 & 19 & -80 \\\\
3 & 70 & 209 & 1+i \\\\
\end{array}$$
{% endcodeblock %}

显示效果为：

$$\begin{array}{c|lcr}
n & \text{Left} & \text{Center} & \text{Right} \\\\
\hline
1 & 1.97 & 5 & 12 \\\\
2 & -11 & 19 & -80 \\\\
3 & 70 & 209 & 1+i \\\\
\end{array}$$

表格的插入也可以使用以下方式：

{% codeblock %}
|名称|说明|
|---|---|
|temperature|  室内温度|
|set temperature|  设定温度|
|height|  室内高度|
{% endcodeblock %}
显示效果：

|名称|说明|
|---|---|
|temperature|  室内温度|
|set temperature|  设定温度|
|height|  室内高度|

矩阵显示和表格很相似

{% codeblock %}
$$\left[
\begin{matrix}
V_A \\\\
V_B \\\\
V_C \\\\
\end{matrix}
\right] =
\left[
\begin{matrix}
1 & 0 & L \\\\
-cosψ & sinψ & L \\\\
-cosψ & -sinψ & L
\end{matrix}
\right]
\left[
\begin{matrix}
V_x \\\\
V_y \\\\
W \\\\
\end{matrix}
\right] $$
{% endcodeblock %}

显示效果为：

$$\left[
\begin{matrix}
V_A \\\\
V_B \\\\
V_C \\\\
\end{matrix}
\right] =
\left[
\begin{matrix}
1 & 0 & L \\\\
-cosψ & sinψ & L \\\\
-cosψ & -sinψ & L
\end{matrix}
\right]
\left[
\begin{matrix}
V_x \\\\
V_y \\\\
W \\\\
\end{matrix}
\right] $$
