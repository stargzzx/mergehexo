---
title: python | Peewee 教程
date: 2020-03-14 22:24:39
categories:
- python
- 模块
- peewee
tags:
- python
- peewee
---
因为公司的量化项目使用的是这个包，所以，在这里我将要详细的解读一下 peewee。

<!-- more -->

<br/>

# 参考资料

<br/>

[Python Peewee 教程](https://geek-docs.com/python/python-tutorial/python-peewee.html)
[基本语法](http://ladder1984.github.io/post/peewee-note1/)

<br/>

# 概述

<br/>

Peewee 是一个简单而小型的 Python ORM 工具。 它支持 SQLite，MySQL 和 PostgreSQL。

关于什么是 ORM 请参考我下面的博文。

[mysql | ORM 详解](https://benpaodewoniu.github.io/2020/03/12/mysql6/)

<br/>

# Peewee 映射

<br/>

- Model映射到数据库表
- Field映射到表列
- instance映射到表行

Peewee 对于 MySQL 使用MySQLDatabase，对于 PostgreSQL 使用PostgresqlDatabase，对于 SQLite 使用SqliteDatabase。

在本教程中，我们将使用 SQLite 数据库。

<br/>

# Peewee 字段类型

<br/>

Peewee 模型中的字段类型定义模型的存储类型。 它们被转换为相应的数据库列类型。

|字段类型	|SQLite|	PostgreSQL|	MySQL|
|---|---|---|---|
|CharField|	varchar|	varchar|	varchar|
|TextField	|text|	text|	longtext|
|DateTimeField	|datetime|	timestamp|	longtext|
|IntegerField|	integer|	integer|	integer|
|BooleanField|	smallint|	bool|	bool|
|FloatField	|real|	real|	real|
|DoubleField|	real|	double|	double|
|BigIntegerField|	integer|	bigint	|bigint|
|DecimalField|	decimal	|numeric	|numeric|
|PrimaryKeyField|	integer|	serial|	integer|
|ForeignKeyField|	integer	|integer|	integer|
|DateField|	date|	date|	date|
|TimeField|	time|	time|	time|


<br/>

# Peewee 模型定义

<br/>

在第一个示例中，我们创建一个简单的数据库表。

- model_definition.py

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

Note.create_table()

note1 = Note.create(text='Went to the cinema')
note1.save()

note2 = Note.create(text='Exercised in the morning',
        created=datetime.date(2018, 10, 20))
note2.save()

note3 = Note.create(text='Worked in the garden',
        created=datetime.date(2018, 10, 22))
note3.save()

note4 = Note.create(text='Listened to music')
note4.save()
```

该示例在 SQLite 中创建notes数据库表。

```python
db = peewee.SqliteDatabase('test.db')
```

我们启动test.db SQLite 数据库。 这将在文件系统上创建一个test.db文件。

```python
class Note(peewee.Model):
...
```

我们定义了一个名为Note的数据库模型。 Peewee 模型继承自peewee.Model。

```python
text = peewee.CharField()
created = peewee.DateField(default=datetime.date.today)
```

我们指定模型字段。 我们有一个CharField和一个DateField。 CharField是用于存储字符串的字段类。 DateField是用于存储日期的字段类。 如果未指定，则采用默认值。

```python
class Meta:
    database = db
    db_table = 'notes'
```

在Meta类中，我们定义对数据库的引用和数据库表名称。

```python
Note.create_table()
```

该表是使用create_table()从模型创建的。

```python
note1 = Note.create(text='Went to the cinema')
note1.save()
```

我们创建并保存一个新实例。

<br/>

# 查

<br/>

peewee中查询记录分为get（查询单条记录）、select(条件查询批量记录)。

## 查询单条记录

注：get方法获取不存在的记录会抛出异常。

- Product.get(name='abc') 或者 Product.get(Product.name=='abc')
- Product.select().where(Product.name=='abc').get()
- Product.select().where(Product.name=='abc').first()

注: 1. get()支持name=‘abc’也支持Product.name==‘abc’的写法，where只Product.name==‘abc’。 2. 前两种都是get方法，查询不到会抛出异常，select方法查询不到返回None。

## 查询全部

Product.select()等价于Django中的Product.select().all()，返回一个可迭代的SelectQuery对象。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

notes = Note.select()

for note in notes:
    print('{} on {}'.format(note.text, note.created))
```

## 条件查询

where()方法可以根据给定条件过滤数据。

我们将两个 where 表达式与&运算符结合使用。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

notes = Note.select().where((Note.id > 2) & (Note.id < 6))

for note in notes:
    print('{} {} on {}'.format(note.id, note.text, note.created))
```

- 或(OR)查询：|，例如：User.select().where((User.is_staff == True) | (User.is_superuser == True))
- 且(AND)查询:&，例如：Product.select().where(Product.name=='abc',Product.owner_id==10086) 或者 Product.select().where((Product.name=='abc') & (Product.owner_id==10086))
- 非(NOT)：~,没用过。。。

## 选择特定的列

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

notes = Note.select(Note.text, Note.created).limit(2)

output = [e for e in notes.tuples()]
print(output)
```

<br/>

# 删

<br/>

## 删除表

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:
        database = db
        db_table = 'notes'

Note.drop_table()
```

## 删除实例

delete_by_id()方法删除由其 ID 标识的实例。 它返回已删除实例的数量。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:
        database = db
        db_table = 'notes'

n2 = Note.delete_by_id(1)
print(n2)
```

## 删除多个实例

要删除更多实例，我们调用delete()方法。 它返回成功删除的实例数。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:
        database = db
        db_table = 'notes'

query = Note.delete().where(Note.id > 3)
n = query.execute()

print('{} instances deleted'.format(n))
```

<br/>

# 增

<br/>

## create()

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

Note.create_table()

note1 = Note.create(text='Went to the cinema')
note1.save()
```

## insert_many()

insert_many()方法允许进行批量创建。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

Note.create_table()

data = [
    { 'text': 'Tai chi in the morning', 'created': datetime.date(2018, 10, 20) },
    { 'text': 'Visited friend', 'created': datetime.date(2018, 10, 12) },
    { 'text': 'Went to cinema', 'created': datetime.date(2018, 10, 5) },
    { 'text': 'Listened to music', 'created': datetime.date(2018, 10, 28) },
    { 'text': 'Watched TV all day', 'created': datetime.date(2018, 10, 14) },
    { 'text': 'Worked in the garden', 'created': datetime.date(2018, 10, 22) },
    { 'text': 'Walked for a hour', 'created': datetime.date(2018, 10, 28) }
]

with db.atomic():

    query = Note.insert_many(data)
    query.execute()
```

<br/>

# 改

<br/>

## update()

update()方法更新实例。 它返回成功更新的实例数。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:
        database = db
        db_table = 'notes'

query = Note.update(created=datetime.date(2018, 10, 27)).where(Note.id == 1)
n = query.execute()

print('# of rows updated: {}'.format(n))
```

<br/>

# 其他

<br/>

## count()

要计算表中的模型实例数，我们可以使用count()方法。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

n = Note.select().count()
print(n)

n2 = Note.select().where(Note.created >= datetime.date(2018, 10, 20)).count()
print(n2)
```

## sql()

可以使用sql()方法显示生成的 SQL 语句。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

note3 = Note.select().where(Note.id == 3)
print(note3.sql())
```

该示例显示将 ORM 查询转换为的 SQL。

```sql
('SELECT "t1"."id", "t1"."text", "t1"."created" FROM "notes" AS "t1" WHERE ("t1"."id" = ?)', [3])
```

## offset和limit

通过offset和limit属性，我们可以定义实例的初始跳过和要包含在select()中的实例数。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'notes'

notes = Note.select().offset(2).limit(3)

for note in notes:
    print(note.id, note.text, note.created)
```

## order_by()

可以使用order_by()对检索到的实例进行排序。

要按升序检索笔记，我们在字段上附加desc()方法。

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Note(peewee.Model):

    text = peewee.CharField()
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db 
        db_table = 'notes'

print('Ascending order')
print('*****************************')

notes = Note.select(Note.text, Note.created).order_by(Note.created)

for note in notes:
    print(note.text, note.created)

print()
print('Descending order')
print('*****************************')

notes = Note.select(Note.text, Note.created).order_by(Note.created.desc())

for note in notes:
    print(note.text, note.created)
```

<br/>

# 一对多关系

<br/>

我们将模型映射到现有表。 使用ForeignKeyField创建模型之间的关系。

```sql
BEGIN TRANSACTION;
DROP TABLE IF EXISTS reservations;
DROP TABLE IF EXISTS customers;

CREATE TABLE IF NOT EXISTS customers(id INTEGER PRIMARY KEY, name TEXT);
INSERT INTO customers(Name) VALUES('Paul Novak');
INSERT INTO customers(Name) VALUES('Terry Neils');
INSERT INTO customers(Name) VALUES('Jack Fonda');
INSERT INTO customers(Name) VALUES('Tom Willis');

CREATE TABLE IF NOT EXISTS reservations(id INTEGER PRIMARY KEY, 
    customer_id INTEGER, created DATE, 
    FOREIGN KEY(customer_id) REFERENCES customers(id));
INSERT INTO reservations(customer_id, created) VALUES(1, '2018-22-11');
INSERT INTO reservations(customer_id, created) VALUES(2, '2018-28-11');
INSERT INTO reservations(customer_id, created) VALUES(2, '2018-29-11');
INSERT INTO reservations(customer_id, created) VALUES(1, '2018-29-11');
INSERT INTO reservations(customer_id, created) VALUES(3, '2018-02-12');
COMMIT;
```

- one2many.py

```python
#!/usr/bin/env python3

import peewee
import datetime

db = peewee.SqliteDatabase('test.db')

class Customer(peewee.Model):

    name = peewee.TextField()

    class Meta:

        database = db
        db_table = 'customers'

class Reservation(peewee.Model):

    customer = peewee.ForeignKeyField(Customer, backref='reservations')
    created = peewee.DateField(default=datetime.date.today)

    class Meta:

        database = db
        db_table = 'reservations'

customer = Customer.select().where(Customer.name == 'Paul Novak').get()

for reservation in customer.reservations:

    print(reservation.id)
    print(reservation.created)
```

在示例中，我们定义了两个映射到表的模型。 然后，我们选择一个客户并显示其预订。

```python
customer = peewee.ForeignKeyField(Customer, backref='reservations')
```

Customer和Reservation模型之间的关系是通过ForeignKeyField创建的。 backref属性设置了我们如何参考客户的预订。

```python
for reservation in customer.reservations:
```

客户实例具有reservations属性，其中包含相应的预留。

```python
1
2018-22-11
4
2018-29-11
```

这是输出。