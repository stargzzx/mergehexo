---
title: sql | 系统展示
date: 2020-02-09 12:22:53
categories:
- mysql
tags:
- mysql
- 数据库
---
我将好好的系统的读一遍书籍 《sql 必知必会》，然后将知识点整理记录下来，以便后续翻阅。

当然这里的内容以我本人为主，我特别熟悉的 sql 语法也不会再提及了。

在这里特别说明，以下语法可以在 mysql 进行使用，但是，换一个 dbms 可能就不行了。

<!-- more -->

<br/>
# DBMS 的特性
<br/>

<li style="margin-left: 40px;font-size: 15px">不区分大小写，但是，有的数据库的列名区分大小写</li><li style="margin-left: 40px;font-size: 15px">列的排列下标从 1 开始，行的排列下标从 0 开始</li><li style="margin-left: 40px;font-size: 15px">sql语句自动忽略所有空格</li>

<br/>
# 检索
<br/>

## 特性

<li style="margin-left: 40px;font-size: 15px">数值 检索的时候可以为 5</li><li style="margin-left: 40px;font-size: 15px">字符 要加上 'string'</li>

## 检索唯一不同值 DISTINCT

有的列的值可能有很多相同的，比如位于哪个省，如果我们想要将唯一字段的值查出来，过滤掉同一字段的

	select distinct live_area from person;

但是，如果我们使用了多字段

	select distinct live_area,name from person;

这个是不起作用的，因为两列完全不同，所以，所有行都会被检索出来。

## 限制行数 limit offset

limit 是限制行数，offset 是偏移。

offset 5 由于行数一开始的下标是 0，则这个意思是从第 6 行开始，也包括第 6 行。

	select * from person limit 5 offset 5;

假设一共有 9 个数据，则上面的检索语句只能拿到 倒数 4 行的。

上面的检索语句等同于

	select * from person limit 5,5;

<br/>
# 注释
<br/>

## 行内注释

	select * -- 注释
	from person

## 行间注释

	# 注释
	select * from 
	person

## 多行注释

	/*
	注释
	*/
	select * from person;

<br/>
# 排序
<br/>

## 指定排序属性

order by

### 单行排序

无论怎么样，这句话必须要放到 sql 语句最后面。

	select * from person order by live_area;

### 多行排序

	select * from person order by live_area,name;

这里面的排序规则是：

如果多条 live_area 有相同，则这几条的排序规则根据 name 来。

## 排序方向

<li style="margin-left: 40px;font-size: 15px">desc 降序</li><li style="margin-left: 40px;font-size: 15px">asc 升序（默认</li>

值得注意的是，这两个标志必须放在属性后面

	select * from live_area desc,name;

返回的语句其排序顺序是 live_area 是降序，name 是升序

如果，全部想 降序，则语句应该是下面的方式

	select * from live_area desc,name desc;

关于这个排序是否区分大小写，主要是和数据库的设计有关，比如 a 是和 A 等价，还是排在 Z 之后。

<br/>
# 过滤
<br/>

操作符

<li style="margin-left: 40px;font-size: 15px">between</li><li style="margin-left: 40px;font-size: 15px">is null</li>

## between

	select * from person where age between 5 and 9;

