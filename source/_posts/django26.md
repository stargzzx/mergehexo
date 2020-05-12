---
title: Django模型层之数据库迁移
date: 2019-09-13 18:12:28
categories:
- python
- 框架
- django
tags:
- Django
---
Django模型层之数据库迁移

<!-- more -->

<br/>

# 迁移的必要性

<br/>

SQLite3在项目中开发初期提供便利，在早期我们将数据存在 SQLite3中。

但是，SQLite3是一个文件数据库，所以性能不好。

所以，要将SQLite3转到常用数据库，比如：MySQL。

<br/>

# Django ORM框架

<br/>

![](/images/django/26_0.png)

<br/>

# 迁移的过程

<br/>

数据库最重要的是

	数据
	表结构
	
迁移分为三个部分

	数据备份
	表结构同步
	数据迁移
	
## 数据备份

	python manage.py dumpdata app的名字(定义了model的应用) > wenjianming.json
	
将数据备份到 json 中。

## 表结构同步

分为三个步骤

	创建MySQL数据库并更新配置
	创建 slave 数据库
	迁移数据库表
	
### 创建MySQL数据库并更新配置

我利用 Navicat ，来创建了一个数据库

![](/images/django/26_1.png)

![](/images/django/26_2.png)

然后我们创建一个叫做 chazuo 的数据库

![](/images/django/26_3.png)

我们在项目的 setting.py 中配置

	DATABASES = {
		'default': {
			'ENGINE': 'django.db.backends.mysql',
			'NAME': 'chazuo',
			'USER':'root',
			'PASSWORD':'',
			'HOST':'127.0.0.1',
			'PORT':'3306'
		}
	}

但是，这个配置目前我们暂时用不到，因为我们之前用的都是默认的 SQLite ，所以，我们要先把这个配置注释掉，然后，利用Django的数据库迁移操作。

将SQLite的表结构迁移到MySQL中。

### 创建 slave 数据库

在 setting.py 将上面的数据直接放到原来的数据库配置中

	DATABASES = {
		'default': {
			'ENGINE': 'django.db.backends.sqlite3',
			'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
		},
		'slave': {
				'ENGINE': 'django.db.backends.mysql',
				'NAME': 'chazuo',
				'USER':'root',
				'PASSWORD':'',
				'HOST':'127.0.0.1',
				'PORT':'3306'
		}
	}

在Django的ORM框架中可以同时支持多个数据库，而default数据库是默认的数据库，而slave数据库是Django的备份数据库，如果 default 数据库挂了，那么才会使用 slave 数据库。

但是，这两个的数据库是同步的。

### 迁移数据库表

	python manage.py migrate --run-syncdb --database slave
		--run-syncdb 表示同步数据库的意思
		--database 同步数据库的时候，目的数据库是什么
		
但是，执行这句话的时候，可能会出现下面的错误：

![](/images/django/26_4.png)

这是因为Django 在连接 MySQL 数据库时默认使用的是 MySQLdb 驱动，然而我们没有安装该驱动，因为它并不支持 Python3，我们现在安装的是 PyMySQL 驱动。

	pip install pyMySQL

在Django的 __init__.py 中写入：

	import pymysql
	pymysql.install_as_MySQLdb()
	
然后再次执行那个迁移命令，我们就能够看到数据列表都已经迁移到 MySQL中了。

![](/images/django/26_5.png)

## 数据迁移

经历过上面的数据库表结构的迁移后，我们就可以把上面的那个注释掉，而启用最开始的那个配置。

![](/images/django/26_6.png)

![](/images/django/26_7.png)

我们先执行数据的备份，我用命令

	python manage.py dumpdata user > user.json

可能会出现下面的错误：

![](/images/django/26_8.png)

这是因为我们没有声明app，所以修改 setting.py 

![](/images/django/26_9.png)

又出现了下面的错误

![](/images/django/26_10.png)

最后我没有按照网上的教程来，因为我觉得有点问题，后来我怀疑是因为没有将数据列表进行更行导致的，所以，我依次运行了下面的两个命令：

	python manage.py makemigrations
	python manage.py migrate
	
最后在运行上面的命令的时候，成功了

![](/images/django/26_11.png)

![](/images/django/26_12.png)

最后我们就会发现多出了一个上面的 json 文件。

截止到现在，我还是很懵逼的，因为，我目前还不了解，所以，我将会继续跟踪，然后完善这个博客。

接下来就是数据迁移的命令了

	python manage.py loaddata user.json
	
这个时候，这条命令又出现错误了

![](/images/django/26_13.png)

我个人猜测，可能是因为原来的数据表没有数据导致的，所以，下面我将还原进行数据导入，然后再复现一边。


