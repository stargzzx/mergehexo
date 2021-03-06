---
title: 编码简介
date: 2018-06-13 14:36:28
categories:
- 电脑相关
- 编程基础
tags:
- ASCII
- 编码
- basis
- knowledge
---
这里简单的对编码有关的知识做个介绍。

<!--more-->

ASCII 是美国人最开始发明的编码 一个字节 只能表示英文字符。

后来中国人为了表示中文，发明了GB2312。

再后来，为了统一编码界的标准，因为各国用的编码不一样，美国用ASCII，中国用GB2312，发明了unicode，这套编码可以表示世界上绝大部分文字。

但是unicode的缺陷很明显，由于每个字符需要双字节，所以假如在表示一份全英文文件时，会比ASCII编码耗费一倍的空间。

所以utf-8应运而生，其字母为单字节，汉字为3字节，特别生僻的未4-6字节。

所以utf-8保存会少很多空间，但是由于有的字母占一个字节，有占多个字节，所以在内存中读取是很不方便。

所以在读取文件时需要将utf-8转变为unicode。

在保存时需要用utf-8保存。
