---
title: python DB 模块
date: 2018-06-08 12:17:36
categories:
- python
tags:
- DB模块
- python 模块
- python
- tutorial
---
python 中的模块。

<!-- more -->

#D

#DB PyMySQL(3 以上版本)

```python
DB 模块(只支持 3 以下版本)
	开始
	创建connection
	获取cursor
	。。。
	关闭cursor
	关闭connrction
	结束
	
在使用 PyMySQL 之前，我们需要确保 PyMySQL 已安装。
PyMySQL 下载地址：https://github.com/PyMySQL/PyMySQL。
如果还未安装，我们可以使用以下命令安装最新版的 PyMySQL：

$ pip install PyMySQL
现在用pymysql模块
	connection 数据库连接对象
	cursor	数据库交互对象
	exceptions 数据库异常类
	conn = pymysql.connect(host='127.0.0.1', port=3306, user='root', passwd='', db='tkq1', charset='utf8')

	# 创建游标
	游标对象：用于执行查询和获取结果
	cursor = conn.cursor()



	# 使用 execute()  方法执行 SQL 查询 
	cursor.execute("SELECT VERSION()")
	# 使用 execute() 方法执行 SQL，如果表存在则删除
	cursor.execute("DROP TABLE IF EXISTS EMPLOYEE")
	# 使用预处理语句创建表

	sql = """CREATE TABLE EMPLOYEE (
        	FIRST_NAME  CHAR(20) NOT NULL,
         	LAST_NAME  CHAR(20),
         	AGE INT,  
         	SEX CHAR(1),
         	INCOME FLOAT )"""
	cursor.execute(sql)

 	# 使用 fetchone() 方法获取单条数据.
	data = cursor.fetchone()

# SQL 插入语句
	sql = """INSERT INTO EMPLOYEE(FIRST_NAME,
         	LAST_NAME, AGE, SEX, INCOME)
         	VALUES ('Mac', 'Mohan', 20, 'M', 2000)"""
	try:
   		# 执行sql语句
   		cursor.execute(sql)
   		# 提交到数据库执行
   		db.commit()
	except:
   		# 如果发生错误则回滚
   		db.rollback()
	# 提交，不然无法保存新建或者修改的数据
	conn.commit()
	# 关闭游标
	cursor.close()
	# 关闭连接
	conn.close()
	
游标的使用
	获取缓冲区内的数据行数（执行cursor.execute（）方法后会把数据缓冲到缓冲区）
	cursor.rowcount
	# 执行SQL，返回受影响行数
	effect_row = cursor.execute("select * from tb7")
	# 获取剩余结果的第一行数据
	row_1 = cursor.fetchone()
	# 获取剩余结果前n行数据
	row_2 = cursor.fetchmany(3)
	# 获取剩余结果所有数据
	row_3 = cursor.fetchall()

在执行cursor.execute（）语句时
	如果出现异常
		则conn.rollback() 回滚事务
	如果没出现
		则conn.commit() 提交事务

事务 ： 访问和更新数据库的一个程序的执行单元
	开发中使用事务
		首先要自动关闭事务（commit）
			conn.autocommit(false)
		正常结束事务
			比如执行（更改） insert 操作，就需要用下面命令提交执行，否则无法生效
			conn.commit()
		异常结束事务（会使上述所有的执行都没有生效）
			conn.rollback()
```

![](/images/python/8_DB.JPG)
