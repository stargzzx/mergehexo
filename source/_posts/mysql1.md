---
title: mysql 的索引
date: 2019-10-27 15:40:38
categories:
- mysql
tags:
- mysql
- 数据库
---
这个很重要，理解他，可以让你对数据库运用的更加熟练。

<!-- more -->

<br/>

# 参考资料

<br/>

[深入浅出数据库索引原理](https://www.cnblogs.com/aspwebchh/p/6652855.html)
[MYSQL-索引](https://segmentfault.com/a/1190000003072424)
[mysql创建多列组合唯一索引，unique index](https://blog.csdn.net/chenxun_2010/article/details/76686318)
[MySQL中添加唯一约束和联合唯一约束](https://blog.csdn.net/yumushui/article/details/38960619)
[MySQL设置多列为唯一约束,即多列不能同时相同](https://blog.csdn.net/itccccc/article/details/77771720)
[mysql 查询效率优化之 常用索引的几种类型 新手使用教程，少走弯路？](https://www.jianshu.com/p/42a08cf3358c)

<br/>

# 正文

<br/>

## 索引存储分类

索引是在MYSQL的存储引擎层中实现的，而不是在服务层实现的。所以每种存储引擎的索引都不一定完全相同，也不是所有的存储引擎都支持所有的索引类型。MYSQL目前提供了一下4种索引。

	B-Tree 索引：最常见的索引类型，大部分引擎都支持B树索引。
	HASH 索引：只有Memory引擎支持，使用场景简单。
	R-Tree 索引(空间索引)：空间索引是MyISAM的一种特殊索引类型，主要用于地理空间数据类型。
	Full-text (全文索引)：全文索引也是MyISAM的一种特殊索引类型，主要用于全文索引，InnoDB从MYSQL5.6版本提供对全文索引的支持。

## 索引中 B树结构的讲解

下面我就根据上面的参考资料进行整合。

使用索引很简单，只要能写创建表的语句，就肯定能写创建索引的语句，要知道这个世界上是不存在不会创建表的服务器端程序员的。然而， 会使用索引是一回事， 而深入理解索引原理又能恰到好处使用索引又是另一回事，这完全是两个天差地别的境界（我自己也还没有达到这层境界）。很大一部份程序员对索引的了解仅限于到“加索引能使查询变快”这个概念为止。

	为什么要给表加上主键？
	为什么加索引后会使查询变快？
	为什么加索引后会使写入、修改、删除变慢？
	什么情况下要同时在两个字段上建索引？

这些问题他们可能不一定能说出答案。知道这些问题的答案有什么好处呢？如果开发的应用使用的数据库表中只有1万条数据，那么了解与不了解真的没有差别， 然而， 如果开发的应用有几百上千万甚至亿级别的数据，那么不深入了解索引的原理， 写出来程序就根本跑不动，就好比如果给货车装个轿车的引擎，这货车还能拉的动货吗？

接下来就讲解一下上面提出的几个问题，希望对阅读者有帮助。

网上很多讲解索引的文章对索引的描述是这样的「索引就像书的目录， 通过书的目录就准确的定位到了书籍具体的内容」，这句话描述的非常正确， 但就像脱了裤子放屁，说了跟没说一样，通过目录查找书的内容自然是要比一页一页的翻书找来的快，同样使用的索引的人难到会不知道，通过索引定位到数据比直接一条一条的查询来的快，不然他们为什么要建索引。

想要理解索引原理必须清楚一种数据结构「平衡树」(非二叉)，也就是b tree或者 b+ tree，重要的事情说三遍：“平衡树，平衡树，平衡树”。当然， 有的数据库也使用哈希桶作用索引的数据结构 ， 然而， 主流的RDBMS都是把平衡树当做数据表默认的索引数据结构的。

我们平时建表的时候都会为表加上主键， 在某些关系数据库中， 如果建表时不指定主键，数据库会拒绝建表的语句执行。 事实上， 一个加了主键的表，并不能被称之为「表」。一个没加主键的表，它的数据无序的放置在磁盘存储器上，一行一行的排列的很整齐， 跟我认知中的「表」很接近。如果给表上了主键，那么表在磁盘上的存储结构就由整齐排列的结构转变成了树状结构，也就是上面说的「平衡树」结构，换句话说，就是整个表就变成了一个索引。没错， 再说一遍， 整个表变成了一个索引，也就是所谓的「聚集索引」。 这就是为什么一个表只能有一个主键， 一个表只能有一个「聚集索引」，因为主键的作用就是把「表」的数据格式转换成「索引（平衡树）」的格式放置。

![](/images/mysql/1_0.jpg)

上图就是带有主键的表（聚集索引）的结构图。图画的不是很好， 将就着看。其中树的所有结点（底部除外）的数据都是由主键字段中的数据构成，也就是通常我们指定主键的id字段。最下面部分是真正表中的数据。 假如我们执行一个SQL语句：

	select * from table where id = 1256;

首先根据索引定位到1256这个值所在的叶结点，然后再通过叶结点取到id等于1256的数据行。 这里不讲解平衡树的运行细节， 但是从上图能看出，树一共有三层， 从根节点至叶节点只需要经过三次查找就能得到结果。如下图

![](/images/mysql/1_1.jpg)

假如一张表有一亿条数据 ，需要查找其中某一条数据，按照常规逻辑， 一条一条的去匹配的话， 最坏的情况下需要匹配一亿次才能得到结果，用大O标记法就是O(n)最坏时间复杂度，这是无法接受的，而且这一亿条数据显然不能一次性读入内存供程序使用， 因此， 这一亿次匹配在不经缓存优化的情况下就是一亿次IO开销，以现在磁盘的IO能力和CPU的运算能力， 有可能需要几个月才能得出结果 。如果把这张表转换成平衡树结构（一棵非常茂盛和节点非常多的树），假设这棵树有10层，那么只需要10次IO开销就能查找到所需要的数据， 速度以指数级别提升，用大O标记法就是O(log n)，n是记录总树，底数是树的分叉数，结果就是树的层次数。换言之，查找次数是以树的分叉数为底，记录总数的对数，用公式来表示就是

![](/images/mysql/1_2.png)

用程序来表示就是Math.Log(100000000,10)，100000000是记录数，10是树的分叉数（真实环境下分叉数远不止10）， 结果就是查找次数，这里的结果从亿降到了个位数。因此，利用索引会使数据库查询有惊人的性能提升。

然而， 事物都是有两面的， 索引能让数据库查询数据的速度上升， 而使写入数据的速度下降，原因很简单的， 因为平衡树这个结构必须一直维持在一个正确的状态， 增删改数据都会改变平衡树各节点中的索引数据内容，破坏树结构， 因此，在每次数据改变时， DBMS必须去重新梳理树（索引）的结构以确保它的正确，这会带来不小的性能开销，也就是为什么索引会给查询以外的操作带来副作用的原因。

讲完聚集索引 ， 接下来聊一下非聚集索引， 也就是我们平时经常提起和使用的常规索引。

非聚集索引和聚集索引一样， 同样是采用平衡树作为索引的数据结构。索引树结构中各节点的值来自于表中的索引字段， 假如给user表的name字段加上索引 ， 那么索引就是由name字段中的值构成，在数据改变时， DBMS需要一直维护索引结构的正确性。如果给表中多个字段加上索引 ， 那么就会出现多个独立的索引结构，每个索引（非聚集索引）互相之间不存在关联。 如下图

![](/images/mysql/1_3.jpg)

每次给字段建一个新索引， 字段中的数据就会被复制一份出来， 用于生成索引。 因此， 给表添加索引，会增加表的体积， 占用磁盘存储空间。

非聚集索引和聚集索引的区别在于， 通过聚集索引可以查到需要查找的数据， 而通过非聚集索引可以查到记录对应的主键值 ， 再使用主键的值通过聚集索引查找到需要的数据，如下图

![](/images/mysql/1_4.jpg)

不管以任何方式查询表， 最终都会利用主键通过聚集索引来定位到数据， 聚集索引（主键）是通往真实数据所在的唯一路径。

然而， 有一种例外可以不使用聚集索引就能查询出所需要的数据， 这种非主流的方法 称之为「覆盖索引」查询， 也就是平时所说的复合索引或者多字段索引查询。 文章上面的内容已经指出， 当为字段建立索引以后， 字段中的内容会被同步到索引之中， 如果为一个索引指定两个字段， 那么这个两个字段的内容都会被同步至索引之中。

先看下面这个SQL语句

	//建立索引
	create index index_birthday on user_info(birthday);

	//查询生日在1991年11月1日出生用户的用户名
	select user_name from user_info where birthday = '1991-11-1'

这句SQL语句的执行过程如下

首先，通过非聚集索引index_birthday查找birthday等于1991-11-1的所有记录的主键ID值

然后，通过得到的主键ID值执行聚集索引查找，找到主键ID值对就的真实数据（数据行）存储的位置

最后， 从得到的真实数据中取得user_name字段的值返回， 也就是取得最终的结果

我们把birthday字段上的索引改成双字段的覆盖索引

	create index index_birthday_and_user_name on user_info(birthday, user_name);

这句SQL语句的执行过程就会变为

通过非聚集索引index_birthday_and_user_name查找birthday等于1991-11-1的叶节点的内容，然而， 叶节点中除了有user_name表主键ID的值以外， user_name字段的值也在里面， 因此不需要通过主键ID值的查找数据行的真实所在， 直接取得叶节点中user_name的值返回即可。 通过这种覆盖索引直接查找的方式， 可以省略不使用覆盖索引查找的后面两个步骤， 大大的提高了查询性能，如下图

![](/images/mysql/1_5.jpg)

下面我们通过具体的索引类型再来深入的讲解。

## mysql的索引大致分类

### 普通索引

这是最基本的索引类型，而且它没有唯一性之类的限制。普通索引可以通过以下几种方式创建：

	创建索引: CREATE INDEX 索引名 ON 表名(列名1，列名2,...);
	修改表: ALTER TABLE 表名ADD INDEX 索引名 (列名1，列名2,...);
	创建表时指定索引：CREATE TABLE 表名 ( [...], INDEX 索引名 (列名1，列名 2,...) );

### UNIQUE索引

表示唯一的，不允许重复的索引，如果该字段信息保证不会重复例如身份证号用作索引时，可设置为unique：

	创建索引：CREATE UNIQUE INDEX 索引名 ON 表名(列的列表);
	修改表：ALTER TABLE 表名ADD UNIQUE 索引名 (列的列表);
	创建表时指定索引：CREATE TABLE 表名( [...], UNIQUE 索引名 (列的列表) );
	
### 主键：PRIMARY KEY索引

主键是一种唯一性索引，但它必须指定为“PRIMARY KEY”。

	主键一般在创建表的时候指定：“CREATE TABLE 表名( [...], PRIMARY KEY (列的列表) ); ”。
	但是，我们也可以通过修改表的方式加入主键：“ALTER TABLE 表名ADD PRIMARY KEY (列的列表); ”。

每个表只能有一个主键。 （主键相当于聚合索引，是查找最快的索引）
注：不能用CREATE INDEX语句创建PRIMARY KEY索引


## 具体详解

### unique 唯一索引

例子1

```python
CREATE TABLE IF NOT EXISTS `ppserver`.`eventTable`(
   `userId`         INT UNSIGNED      NOT null,
   `taskId`         INT UNSIGNED      NOT null,
   `date`           TIMESTAMP         DEFAULT CURRENT_TIMESTAMP,
   `createTime`     TIMESTAMP         DEFAULT CURRENT_TIMESTAMP,
   `endTime`        TIMESTAMP         DEFAULT CURRENT_TIMESTAMP,
   `taskStatus`     INT               DEFAULT NULL,
   `addGoldCoin`    int               DEFAULT NULL,
   `addCash`        FLOAT             DEFAULT NULL,
   `eventId`        INT UNSIGNED AUTO_INCREMENT DEFAULT null,
   unique index(`userId` ,`taskId`,`date`),
   PRIMARY KEY ( `eventId`)
)ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

例子2

```python
CREATE TABLE Persons   
(   
Id_P int NOT NULL,   
LastName varchar(255) NOT NULL,   
FirstName varchar(255),   
Address varchar(255),   
City varchar(255),   
CONSTRAINT uc_PersonID UNIQUE (Id_P,LastName)   
)
```

#### 唯一索引的场景

如果业务中要求两个字符联合起了是唯一的，比如“地址”+“名称”是唯一的，又假设有一个需求是用户给评论点赞,数据库设计是三张表,用户表t_user,评论表t_comment,点赞表t_praise,其中点赞表中有两个外键分别是user_id和comment_id,分别关联用户表的用户id和评论表的评论id,然后规定一个用户只能给同一条评论点赞一次,有一种实现方式就是在插入点赞表之前,先通过user_id和comment_id查询是否有点赞记录,如果没有的话,再执行插入操作,否则返回您已经点过赞了.这样实现的话就会多一次数据库查询操作.更好的实现是,修改点赞表的user_id和comment_id为唯一约束,即这两列不能同时相同,这样在执行插入操作的话,如果已经点过赞了,数据库会抛出违反了唯一键约束,这样的话,就可以避免多一次数据库查询操作了.具体设置多列为唯一约束的语句是:

```python
CREATE TABLE `t_praise` (
  `id` int(12) unsigned NOT NULL AUTO_INCREMENT,
  `comment_id` int(12) NOT NULL,
  `user_id` int(12) NOT NULL,
  KEY `FK_t_praise_comment` (`comment_id`),
  KEY `FK_t_praise_user` (`user_id`),
  UNIQUE KEY `UK_praise` (`comment_id`,`user_id`)

)
```

## mysql中key 、primary key 、unique key 与index区别

```python
CREATE TABLE `phpcolor_ad` (  
`id` mediumint(8) NOT NULL AUTO_INCREMENT,  
`name` varchar(30) NOT NULL,  
`type` mediumint(1) NOT NULL,  
`code` text,  
PRIMARY KEY (`id`),  
KEY `type` (`type`)  
);
```

最后一句的KEY `type` (`type`)是什么意思？

如果只是key的话，就是普通索引。

mysql的key和index多少有点令人迷惑，单独的key和其它关键词结合的key(primary key)实际表示的意义是不同，这实际上考察对数据库体系结构的了解的。

### key

key 是数据库的物理结构，它包含两层意义和作用，

	一是约束（偏重于约束和规范数据库的结构完整性），
	二是索引（辅助查询用的）。

包括primary key, unique key, foreign key 等。

#### primary key 

有两个作用，一是约束作用（constraint），用来规范一个存储主键和唯一性，但同时也在此key上建立了一个主键索引；    

PRIMARY KEY 约束：唯一标识数据库表中的每条记录；

主键必须包含唯一的值；

主键列不能包含 NULL 值；

每个表都应该有一个主键，并且每个表只能有一个主键。（PRIMARY KEY 拥有自动定义的 UNIQUE 约束）

#### unique key

有两个作用，一是约束作用（constraint），规范数据的唯一性，但同时也在这个key上建立了一个唯一索引；

UNIQUE 约束：唯一标识数据库表中的每条记录。

UNIQUE 和 PRIMARY KEY 约束均为列或列集合提供了唯一性的保证。（每个表可以有多个 UNIQUE 约束，但是每个表只能有一个 PRIMARY KEY 约束）

#### foreign key

有两个作用，一是约束作用（constraint），规范数据的引用完整性，但同时也在这个key上建立了一个index；

可见，mysql的key是同时具有constraint和index的意义，这点和其他数据库表现的可能有区别。

（至少在Oracle上建立外键，不会自动建立index），因此创建key也有如下几种方式：

	在字段级以key方式建立， 如 create table t (id int not null primary key);
	在表级以constraint方式建立，如create table t(id int, CONSTRAINT pk_t_id PRIMARY key (id));
	在表级以key方式建立，如create table t(id int, primary key (id));

其它key创建类似，但不管那种方式，既建立了constraint，又建立了index，只不过index使用的就是这个constraint或key。

### index

index是数据库的物理结构，它只是辅助查询的，它创建时会在另外的表空间（mysql中的innodb表空间）以一个类似目录的结构存储。索引要分类的话，分为前缀索引、全文本索引等；

因此，索引只是索引，它不会去约束索引的字段的行为（那是key要做的事情）。

	如：create table t(id int,index inx_tx_id (id));
	
### 总结

总结，最后的释疑：

#### 我们说索引分类，分为

主键索引（必须指定为“PRIMARY KEY”，没有PRIMARY Index）、

唯一索引（unique index，一般写成unique key）、

普通索引(index，只有这一种才是纯粹的index)等，也是基于是不是把index看作了key。

比如 create table t(id int, unique indexinx_tx_id (id));--index当作了key使用


#### 最重要的也就是

不管如何描述，需要理解index是纯粹的index（普通的key，或者普通索引index），还是被当作key（如：unique index、unique key和primary key），若当作key时则会有两种意义或起两种作用。



## 索引选择原则

性能优化过程中，选择在哪个列上创建索引是最非常重要的。可以考虑使用索引的主要有 两种类型的列：在where子句中出现的列，在join子句中出现的列，而不是在SELECT关键字后选择列表的列；

索引列的基数越大，索引的效果越好。例如，存放出生日期的列具有不同的值，很容易区分行，而用来记录性别的列，只有"M"和"F",则对此进行索引没有多大用处，因此不管搜索哪个值，都会得出大约一半的行,（ 见索引选择性注意事项对选择性解释；）

使用短索引，如果对字符串列进行索引，应该指定一个前缀长度，可节省大量索引空间，提升查询速度；

>例如，有一个CHAR(200)列，如果在前10个或20个字符内，多数值是唯一的，那么就不要对整个列进行索引。对前10个或者20个字符进行索引能够节省大量索引空间，也可能会使查询更快。较小的索引涉及的磁盘IO较少，较短的值比较起来更快。更为重要的是，对于较短的键值，所以高速缓存中的快能容纳更多的键值，因此，MYSQL也可以在内存中容纳更多的值。这样就增加了找到行而不用读取索引中较多快的可能性。

利用最左前缀

既然索引可以加快查询速度，那么是不是只要是查询语句需要，就建上索引？答案是否定的。因为索引虽然加快了查询速度，但索引也是有代价的：索引文件本身要消耗存储空间，同时索引会加重插入、删除和修改记录时的负担，另外，MySQL在运行时也要消耗资源维护索引，因此索引并不是越多越好。

一般两种情况下不建议建索引：

1.表记录比较少，例如一两千条甚至只有几百条记录的表，没必要建索引，让查询做全表扫描就好了;

>至于多少条记录才算多，这个个人有个人的看法，我个人的经验是以2000作为分界线，记录数不超过 2000可以考虑不建索引，超过2000条可以酌情考虑索引。

2.索引的选择性较低。所谓索引的选择性（Selectivity），是指不重复的索引值（也叫基数，Cardinality）与表记录数（#T）的比值：