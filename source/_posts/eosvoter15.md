---
title: eosvoter | 数据库操作
date: 2020-06-07 23:32:55
categories:
- [项目经历,中型,eosvoter]
tags:
- eosvoter
---
eosvoter 项目中将数据库操作自己整合成一个小的脚本。

<!-- more -->

## 源代码

eosvoter 的操作数据库的脚本如下。


### db.py

```python
import pymysql
import os
from DBUtils.PooledDB import PooledDB

import app_config as config

db_host = os.environ.get("DB_HOST")
if db_host is None:
    db_host = config.db_host

pool = PooledDB(
    pymysql,
    5,
    host=db_host,
    user=config.db_user,
    passwd=config.db_password,
    db=config.db_database,
    port=config.db_port,
    charset=config.db_charset
)


def __db_query(sql, args=None):
    connector = pool.connection()
    tmp_cursor = connector.cursor()
    tmp_cursor.execute(sql, args)
    data = tmp_cursor.fetchall()
    tmp_cursor.close()
    connector.close()
    return data


def __db_update(sql, args=None):
    connector = pool.connection()
    tmp_cursor = connector.cursor()
    ret = tmp_cursor.execute(sql, args)
    connector.commit()
    tmp_cursor.close()
    connector.close()
    return ret


def get_all(sql, param=None):
    connector = pool.connection()
    _cursor = connector.cursor()
    if param is None:
        count = _cursor.execute(sql)
    else:
        count = _cursor.execute(sql, param)
    if count > 0:
        result = _cursor.fetchall()
    else:
        result = False
    _cursor.close()
    connector.close()
    return result


def get_one(sql, param=None):
    connector = pool.connection()
    _cursor = connector.cursor()
    if param is None:
        count = _cursor.execute(sql)
    else:
        count = _cursor.execute(sql, param)
    if count > 0:
        result = _cursor.fetchone()
    else:
        result = False
    _cursor.close()
    connector.close()
    return result


def insert_one(sql, value):
    connector = pool.connection()
    _cursor = connector.cursor()
    ret = _cursor.execute(sql, value)
    connector.commit()
    _cursor.close()
    connector.close()
    return ret


def insert_many(sql, values):
    connector = pool.connection()
    _cursor = connector.cursor()
    count = _cursor.executemany(sql, values)
    _cursor.close()
    return count


def __get_insert_id():
    connector = pool.connection()
    _cursor = connector.cursor()
    _cursor.execute("SELECT @@IDENTITY AS id")
    result = _cursor.fetchall()
    _cursor.close()
    connector.close()
    return result[0][0]


def __query(sql, param=None):
    connector = pool.connection()
    _cursor = connector.cursor()
    if param is None:
        count = _cursor.execute(sql)
    else:
        count = _cursor.execute(sql, param)
    connector.commit()
    _cursor.close()
    connector.close()
    return count


def update(sql, param=None):
    return __query(sql, param)


def delete(sql, param=None):
    return __query(sql, param)
```

### test.py

```python
import db

class Pool:

    def __init__(self):
        self.db = db

    # 对单一的矿池数据操作
    def add_pool_data(self, pool_name: str, block_coin: str, coin_reward: str, theory_reward: str, block_time: str,
                      pool_power: str,
                      pool_scale: str, diff_24h: str, lucky_3d: str):
        sql = "insert into t_pool_data(`pool_name`,`block_coin`,`coin_reward`,`theory_reward`,`block_time`,`pool_power`,`pool_scale`,`diff_24h`,`lucky_3d`) values (%s,%s,%s,%s,%s,%s,%s,%s,%s);"
        return self.db.insert_one(sql, [pool_name, block_coin, coin_reward, theory_reward, block_time, pool_power,
                                        pool_scale, diff_24h, lucky_3d])

    def get_by_pool_coin_time(self, pool_name: str, block_coin: str, start_time: str, end_time: str):
        sql = "select pool_name,coin_reward,theory_reward,block_time,pool_power,pool_scale,diff_24h,lucky_3d from t_pool_data where pool_name = %s and block_coin = %s and block_time >= %s and block_time <= %s;"
        return self.db.get_all(sql, [pool_name, block_coin, start_time, end_time])

    def get_by_time(self, coin_type, block_time):
        sql = "select count(*) from t_pool_data where block_coin = %s and block_time = %s;"
        ret = self.db.get_one(sql, [coin_type, block_time])
        return ret[0]
```