---
title: ant | 数据库的对接
date: 2020-03-15 19:49:41
categories:
- [项目经历,大型,ant]
tags:
- ant
- coinex
password: antant5
abstract: ant 项目资料
message: 您好, 这里是 2 级加密文章，不对非利益方公开，请理解。
---
目前， ant 项目的数据库使用的是 peewee 。

其在这个的基础上，进行了一些简易的配置，在此做一个总结。

当然，这个解读会简化很多细节问题，毕竟，架构这种东西，关键在于思想。

这里我们仅仅只是以一个数据表为例

- t_baseline

另外，需要说的是，在我写这篇博客的时候，项目的数据库连接代码进行了大规模的精简，现在比之前好多了。

<!-- more -->

关于 ORM 和 peewee 的知识，你可以看我下面的博文。

[mysql | ORM 详解](https://benpaodewoniu.github.io/2020/03/12/mysql6/)
[python | Peewee 教程](https://benpaodewoniu.github.io/2020/03/14/python77/)

<br/>

# 架构

<br/>

我将数据库的操作架构分为三个部分

- 初始化数据库
- peewee 相关代码的编写
- 函数集

<br/>

# 初始化数据库

<br/>

首先， main.py 会读取环境变量，而环境变量中放着我们的配置。

如

	os.environ.get(f"{prefix}_MYSQL_HOST")

关于 pycharm 如何设置环境变量，请参考我下面的博文。

[pycharm的一些技巧-指定参数执行脚本](https://benpaodewoniu.github.io/2019/11/10/pycharm0/)

读取到相关的配置后，我们会运行初始化，这个初始化仅仅只是初始化数据库的连接，按照 peewee 的语法来说是到了这里

	PooledMySQLDatabase(**settings)

<br/>

# peewee 相关代码的编写

<br/>

在这里我使用数据库的其中一张表 t_baseline 。下面是这张表的 py 操作文件。

	base_line.py

这里没什么好说的，直接贴一下代码(相关的细节我已经删除)。

```python
def init_base_line_model(db: Database):
    class DbBaseLine(ModelBase):
        ctime: datetime = DateTimeField()
        utime: datetime = DateTimeField()

        class Meta:
            database = db
            table_name = "t_base_line"

        @staticmethod
        def from_base_line(baseline: BaseLine):
            """
            Generate DbRelation object from Relation.
            """
            db_baseline = DbBaseLine()
            db_baseline.ctime = baseline.ctime
            db_baseline.utime = baseline.utime

            return db_baseline

        def to_baseline(self):
            """
            Generate User object from DbUser.
            """
            baseline = BaseLine(
                ctime=self.ctime,
                utime=self.utime,
            )
            return baseline

        @staticmethod
        def get_baseline(state: int, coin: str) -> Sequence["DbBaseLine"]:
            with db.connection_context():
                s = (
                    DbBaseLine.select().where(
                        (DbBaseLine.state == state)
                        & (DbBaseLine.coin == coin))
                )
            return s

    return DbBaseLine
```

在这里我们要注意以下几点

- DbBaseLine 这个 class 是放在 init_base_line_model 方法里面的
- 里面对这张表的操作是静态方法（这个很重要）

<br/>

# 函数集

<br/>

这个函数集，我认为是数据库操作的精髓。

我们想一下这样的一个场景，假设一个数据库有 N 个数据表，我们要对某一个数据表进行操作，那么势必会构筑相应的对象，然后引入相应的方法。

那么，这样会导致出现一个问题，假设我在不同地方调用，那么我得创建不同的对象，即便是使用单例模式，那么也会使得在多个不同的文件中调用数据表对象进行操作。

并且，即便是使用了单例模式，有这么多数据表，你也得在不同的地方创建数据表对象，或许使用工厂模式会简化一些代码，使其变得具有更高的可读性，但是违背了程序设计的一个原则：

高内聚，低耦合。

关于单例模式、工厂模式，你可以看我下面的博文。

[设计模式 | 单例模式](https://benpaodewoniu.github.io/2020/03/14/designthinkings4/)
[设计模式 | 工厂模式](https://benpaodewoniu.github.io/2020/03/14/designthinkings5/)

那么， ant 项目使用的是继承，如果换成 java 的话，叫做接口更合适。

我们现在先来记住两个文件

- databse.py
- database_sql.py

## databse.py

这个是一个抽象类，里面记载了所有数据表操作的方法，举一个简单的例子。

```python
class BaseDatabaseManager(ABC):

	# t_trade_user 表 里面的一个方法
    def get_trade_user_by_platform(self, plt: str, user_id: int) -> Optional["TradeUser"]:
    pass

    # t_baseline 这张表的一个方法，在上面你可以看到
    def get_baseline(self, state: int, coin: str) -> Sequence["DbBaseLine"]:
        pass
```

## database_sql.py

这个文件是一个核心文件，它将上面讲述的所有模块都集中在一起。

### 初始化数据库和数据表

```python
class SqlManager(BaseDatabaseManager):

	    def __init__(self, settings: dict):
        db = PooledMySQLDatabase(**settings)
        self.tick_recordings = init_tick_recordings_model(db)
        self.base_line = init_base_line_model(db)
```

在这里我们要注意一个细节，就是 SqlManager 继承了 BaseDatabaseManager ，所以，我们要实现 BaseDatabaseManager 里面的方法。

举一个例子，SqlManager 里面的 get_baseline 方法：

```python
def get_baseline(self, state: int, coin: str) -> Sequence["BaseLine"]:
    data = []
    s = (
        self.base_line.get_baseline(state, coin)
    )
    for b in s:
        data.append([b.std_value, b.all_std_value, b.coin, b.data_type, b.cycle])
    return data
```

这个方法，使用了初始化数据表中的方法，也就是 t_baseline 这个对象表的 get_baseline 这个方法。

非常的妙，它将所有的表操作方法，全部归结到一个类中，并且，在这个类的内部对所有的数据表都初始化了，从而可以调用表的方法。

这应该也是 23 种设计模式之一，但是，我现在还不知道是哪一种，这个待更新吧。

然后，假如要获得 t_base_line 的数据，只需要

	databasemanager = SqlManager()
	databasemanager.get_baseline()

就好了，实现了高内聚，低耦合。

如果让我改进的话，我可能会增加单例模式。

在这里我提出两个疑问

1. 为什么数据表类使用静态方法，而不使用实例方法？
2. 为什么要使用函数返回数据表类，而不是，就直接 new ？

我给出我自己的见解，一切都是为了提高性能，或者提高代码可读性。实例化会消耗更多的资源，这是毫无疑问的，而静态方法，就只有一个，它是类方法，是唯一的，在加载中完成，且只加载一次。所以，如果使用实例化，那么必须使用单例模式才能达到一样的效果。

希望可以帮助到各位，谢谢大家。