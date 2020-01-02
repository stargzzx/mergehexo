---
title: machine_learning NG ex3
date: 2018-04-20 17:16:21
categories:
- macheine learning answer
tags:
- macheine learning answer
- machine learning
- Andrew Ng
- answer
---
Code in github.[address](https://github.com/benpaodewoniu/Andrew-Ng-machine-learning)
I will show my thinking about ex3.
 <!-- more -->
 Finally,i post my code there.
### one vs all AND predict
 {% codeblock %}
load('ex3data1.mat'); %导入数据
m = size(X,1);        %获得数据的行数
X = [ones(m,1),X];    %因为有一个偏移值，所以要加一行 m * 1 的向量（其向量值均为1）

function [J,grad] = costFunction(theta,X,y,lambda,m) %构造函数
  h = sigmoid(X*theta);
  theta1 = theta;
  theta1(1) = 0;  %由于正则化不会对偏移值处理，所以要使得梯度下降或者其他算法的时候不要对偏移值进行正则化处理
  J = (-y'*log(h)-(1-y)'*log(1-h)+theta1'*theta1*lambda/2)/m; %正则化公式
  grad = X'*(h-y)/m+lambda/m*theta1; %梯度 
end;

num_labels = 10; %10种分类
all_theta = zeros(10,401); %对每一个数据都需要进行十次分类，最后得出最佳的矩阵值，然后max（A（1，10））中的索引值就可以得到最有可能的值
lambda = 3; %学习率
for k = 1:num_labels  %十次循环，因为要进行十次分类
    initial_theta = zeros(401, 1); %初始化 theta ，比原来多一个是因为有一个偏移值
    options = optimset('GradObj', 'on', 'MaxIter', 50); %优化器
    [theta] = fmincg(@(t)(costFunction(t, X, (y==k), lambda,m)), initial_theta, options);
    % 上面式子中 y==k 是让函数有一个判断标准
    all_theta(k, :) = theta' ;
end  

%运用训练数据作为预测数据，来检验得到的 all_theta 的正确率
y1 = zeros(m,1);
y2 = zeros(m,1);
for j = 1:m,
  [y1(j,:),y2(j,:)] = max((X * all_theta')(j,:));
end;

for k = 1:m,
  y2(k,:) = (y2(k,:) == y(k,:));
end;
y3 = size(find(y2 == 1)) / m;
y3  % 测试后为 93.3%
{% endcodeblock %}
