---
title: mysql | ORM 详解
date: 2020-03-12 15:07:15
categories:
- sql
- mysql
tags:
- mysql
- ORM
---
ORM 是数据库的一种面向对象编程。

不应该归属于 mysql 类，但是，现在 blog 中没有其他的专栏，所以，在这里暂时存放一下 ORM。

因为，我们公司的量化项目使用的是 peewee ，所以，我需要 ORM 的知识。

<!-- more -->

<br/>

# 参考资料

<br/>

[ORM 实例教程](http://www.ruanyifeng.com/blog/2019/02/orm-tutorial.html)

<br/>

# 概述

<br/>

面向对象编程和关系型数据库，都是目前最流行的技术，但是它们的模型是不一样的。

面向对象编程把所有实体看成对象（object），关系型数据库则是采用实体之间的关系（relation）连接数据。很早就有人提出，关系也可以用对象表达，这样的话，就能使用面向对象编程，来操作关系型数据库。

![](/images/mysql/6_0.png)

简单说，ORM 就是通过实例对象的语法，完成关系型数据库的操作的技术，是"对象-关系映射"（Object/Relational Mapping） 的缩写。

ORM 把数据库映射成对象。

- 数据库的表（table） --> 类（class）
- 记录（record，行数据）--> 对象（object）
- 字段（field）--> 对象的属性（attribute）

![](/images/mysql/6_1.png)

举例来说，下面是一行 SQL 语句。

	SELECT id, first_name, last_name, phone, birth_date, sex FROM persons WHERE id = 10

改成 ORM 的写法如下。

	p = Person.get(10);
	name = p.first_name;

一比较就可以发现，ORM 使用对象，封装了数据库操作，因此可以不碰 SQL 语言。开发者只使用面向对象编程，与数据对象直接交互，不用关心底层数据库。

总结起来，ORM 有下面这些优点。

- 数据模型都在一个地方定义，更容易更新和维护，也利于重用代码。
- ORM 有现成的工具，很多功能都可以自动完成，比如数据消毒、预处理、事务等等。
- 它迫使你使用 MVC 架构，ORM 就是天然的 Model，最终使代码更清晰。
- 基于 ORM 的业务代码比较简单，代码量少，语义性好，容易理解。
- 你不必编写性能不佳的 SQL。

但是，ORM 也有很突出的缺点。

- ORM 库不是轻量级工具，需要花很多精力学习和设置。
- 对于复杂的查询，ORM 要么是无法表达，要么是性能不如原生的 SQL。
- ORM 抽象掉了数据库层，开发者无法了解底层的数据库操作，也无法定制一些特殊的 SQL。

<br/>

# 命名规定

<br/>

许多语言都有自己的 ORM 库，最典型、最规范的实现公认是 Ruby 语言的 Active Record。Active Record 对于对象和数据库表的映射，有一些命名限制。

- 一个类对应一张表。类名是单数，且首字母大写；表名是复数，且全部是小写。比如，表books对应类Book。

- 如果名字是不规则复数，则类名依照英语习惯命名，比如，表mice对应类Mouse，表people对应类Person。

- 如果名字包含多个单词，那么类名使用首字母全部大写的骆驼拼写法，而表名使用下划线分隔的小写单词。比如，表book_clubs对应类BookClub，表line_items对应类LineItem。

- 每个表都必须有一个主键字段，通常是叫做id的整数字段。外键字段名约定为单数的表名 + 下划线 + id，比如item_id表示该字段对应items表的id字段。

<br/>

# 实例

<br/>

关于 ORM 的实例，我想以 python 的 peewee 为例。

所以，请看我下面的博文。

[python | Peewee 教程](https://benpaodewoniu.github.io/2020/03/14/python77/)