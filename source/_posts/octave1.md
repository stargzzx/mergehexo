---
title: octave about use of fmincg
date: 2018-04-19 23:27:21
categories:
- octave
tags:
- paint
- octave
- tutorial
---
这篇不是讲专门的讲这个函数的用法，而是讲一下初次看这个函数的时候遇到的一些疑问。

<!-- more -->

 ## @

首先，我想先从 @ 开始讲起。

@ 一共有两个作用。

### 匿名函数中的应用

{% codeblock %}
变量名=@(输入参数列表)运算表达式
例如计算一个数的平方可以写成如下格式：
mysqr=@(x)x.*x;
%第一个（x）为输入的参数，而后面x.*x则为数学意义上的x^2；
执行mysqr(变量名) 即可完成平方计算。例如：
mysqr(2)
  ans = 4
{% endcodeblock %}

### 作为自定义函数的指针（句柄）

{% codeblock %}
我们定义一个这样的m文件：

f.m：
	function  y=f(x)
	y=cos(x);
	
则可以定义mycos = @f 来调用已定义函数。
并通过 mycos(pi)来使用该函数。
返回 ans = -1
{% endcodeblock %}

下面我们来具体讲一下细节方面的展现。

## Example1

我们先定义一个文件 test1.m

test1.m

{% codeblock %}
function[jval,grad] = test1(theta);
...
...
{% endcodeblock %}

定义一个 test2.m

{% codeblock %}
...
...
option1 = optimset('GradObj','on','MaxIter','40');
initalthrta = zeros(2,1);
[optTheta,functionVal,ExitFlag] = fminunc(@test1,initalthrta,option1);
...
...
{% endcodeblock %}

## Example2

定义一个文件 lrCostFunction.m

{% codeblock %}
function [J, grad] = lrCostFunction(theta, X, y, lambda)
...
...
{% endcodeblock %}

在定义一个文件

{% codeblock %}
...
...
initial_theta = zeros(n + 1, 1); 
options = optimset('GradObj', 'on', 'MaxIter', 50);
[theta] = fmincg(@(t)(lrCostFunction(t, X, (y==k), lambda)), initial_theta, options);
...
...
{% endcodeblock %}

可以看出 example1 和 example2 最为突出的不同在于 t。

先在这里贴一张图，函数之间的对应关系。

{% img /images/octave/1.jpg %}

可以看出，我们在 example2 中，第一个 t 只是一个传参，代表的是后面括号里面的函数，第二个 t 也只是象征性的写法，这是由 fmincg 这个方法特性所决定的，我之所以讲不清楚，因为我也是一知半解，但即便是这样，运用它已经不是问题了。