---
title: mysql 修改数据库
date: 2019-11-24 20:46:44
categories:
- sql
- mysql
tags:
- mysql
- 数据库
---
这个是对数据库做修改，比如增加字段，改变字段的名称或者改变字段的属性。

<!-- more -->

<br/>

# 在原来的数据表中增加字段

<br/>

	ALTER TABLE t_claim_info ADD check_df BOOLEAN DEFAULT FALSE;

上面这个是可以的，但是下面这个不行

	ALTER TABLE t_claim_info ADD check BOOLEAN DEFAULT FALSE;

这个不行，因为check不能作为字段名

<br/>

# 删除字段

<br/>

	ALTER TABLE t_claim_info DROP check_df;

<br/>

# 改变字段的属性

<br/>

	alter table t_claim_info modify check_df tinyint default '0';
