---
title: machine_learning NG ex1
date: 2018-04-13 13:40:12
categories:
- macheine learning answer
tags:
- macheine learning answer
- macheine learning
- Andrew Ng
- answer
---
Code in github.[address](https://github.com/benpaodewoniu/Andrew-Ng-machine-learning)
I will show my thinking about ex1.
 <!-- more -->
 ## gradientDescent
 {% img /images/machinelearninganswer/0_0.jpg %}
 So,i write a code to check my thinking.
{% codeblock %}
function test();
data = load('ex1data1.txt');
X = data(:,1);  %向量 X
y = data(:,2);  #实际值 向量
m = length(y);
figure1 = plot(X,y,'rx','MarkerSize',10); %对原有数据可视化

X = [ones(m, 1), data(:,1)]; % Add a column of ones to x
theta = zeros(2, 1); % initialize fitting parameters

% Some gradient descent settings
iterations = 1;     %迭代次数
alpha = 0.01;       %学习率
J = 1/(2 * m) *  sum((X*theta-y).^2);
for iter = 1:iterations
    theta = theta - alpha * (1 / m) * X' * (X * theta - y); 
end
disp(theta);
A = [1 5;1 25];
B = A * theta;
hold on;
plot([5;25],B);
{% endcodeblock %}
In fact,i use iterations for 1,10,100,1000,10000.
Here is picture.
 {% img /images/machinelearninganswer/0_1.jpg %}
