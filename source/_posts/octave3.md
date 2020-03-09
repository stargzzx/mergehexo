---
title: octave绘制图片
date: 2018-06-06 22:11:50
categories:
- octave
tags:
- paint
- octave
- tutorial
---
Here is tutorial of octave.

<!-- more -->

<br/>

# 解析数据

<br/>

假设有 test.txt 存有数据分布如下：

{% codeblock %}
3 3
1 5
3 5
2 7
{% endcodeblock %}

创建一个名为 test.m 的文件，输入以下代码：

{% codeblock %}
data = load('test.txt');
data
size(data)

	data =
		3   3
		1   5
		3   5
		2   7
	ans =
		4   2
所以，octave的数据输入是将一行作为矩阵中的一行，并且其中的空格作为每一列的分隔，将换行符作为每一行的分隔
{% endcodeblock %}

<br/>

# 绘制 2D

<br/>

## 连线图

假设有如下数据

{% codeblock %}
3 6
5 10
{% endcodeblock %}

绘制连线图代码

{% codeblock %}
data = load('test.txt');
plot(data(:,1),data(:,2));
{% endcodeblock %}

![](/images/octave/3_0.jpg)

## 根据公式绘制连线图

{% codeblock %}
W = [3.00774324,1.69532264]
x = 0:0.1:1
y = x * W(:,2) + W(:,1)
plot(x, y, 'k', 'LineWidth', 1.5);
{% endcodeblock %}

![](/images/octave/3_0_0.jpg)

## 特殊形式

如果是个二维数组，那么直接用 plot 传递单个数值，会导致 X 轴以 1 变量递增，而 Y 轴的递增是根据传递的那个数值来的。

{% codeblock %}
data = [[1 2 3]
         [4 5 6]
         [1 2 3]
]
size(data)
figure;
hold on;
plot(data)
hold off;
{% endcodeblock %}

![](/images/octave/3_0_1.jpg)

## 散点图

还是沿用上面的数据，编写下面数据

{% codeblock %}
data = load('test.txt');
plot(data(:,1),data(:,2),'k+','LineWidth',2);
axis([1 6 4 12]); #设置横纵坐标
{% endcodeblock %}

![](/images/octave/3_1.jpg)

<br/>

# 绘制 3D

<br/>

## 散点图

有如下数据：

{% codeblock %}
1 3 5
2 6 9
3 9 12
{% endcodeblock %}

编写如下代码：

{% codeblock %}
data = load('test.txt');
mat = data(:,1:3);
figure;
plot3(mat(:,1),mat(:,2),mat(:,3),'k+','LineWidth',2);
axis([0 4 2 10 4 13])；
{% endcodeblock %}

![](/images/octave/3_2.jpg)







