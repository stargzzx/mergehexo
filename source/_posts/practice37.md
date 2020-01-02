---
title: practice37
date: 2018-08-10 21:08:26
categories:
- practice
tags:
- practice
- algorithm
---
问题描述：
给定一个链表的头结点 head ，再给定一个数 num ，请把链表调整成值小于 num 的节点都放在链表的左边，值等于 num 的都放在链表的中间，值大于 num 的节点，放在链表的右边。
<!-- more -->
## 原理
简单地做法是再申请一个数组，将链表的数据放在数组中，就好像荷兰国旗一样，对数组进行排列，最后在串联起来。
比较好的方法就是，让大中小各组成三个链，然后一点点的组合，最终串联。
{% codeblock %}



{% endcodeblock %}
