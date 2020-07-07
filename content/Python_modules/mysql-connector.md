---
title: "mysql-connector"
date: 2020-06-26 02:25
---
[toc]

# Mysql-connector



## installation

```
pip install mysql-connector
```



## hello world

```
import json
import traceback
import mysql.connector

# 读取数据库链接配置文件
with open('mysql.json', encoding='utf-8') as con_json:
    con_dict = json.load(con_json)

# 打开数据库链接
db = mysql.connector.connect(
    host=con_dict['host'],
    user=con_dict['user'],
    passwd=con_dict['passwd'],
    database=con_dict['database'],
    auth_plugin=con_dict['auth_plugin'],
)

# 获取操作游标
cursor = db.cursor()
try:
    sql = 'SELECT id, name, hp_max FROM heros WHERE hp_max>6000'
    cursor.execute(sql)
    data = cursor.fetchall()
    print(cursor.rowcount, '查询成功。')
    for each_hero in data:
        print(each_hero)
except Exception as e:
    # 打印异常信息
    traceback.print_exc()
finally:
    cursor.close()
    db.close()
```

```
使用cursor.fetchall()，取出数据集中的所有行，返回一个元组 tuples 类型；
使用cursor.fetchmany(n)，取出数据集中的多条数据，同样返回一个元组 tuples；
使用cursor.rowcount，返回查询结果集中的行数。如果没有查询到数据或者还没有查询，则结果为 -1，否则会返回查询得到的数据行数；
使用cursor.close()，关闭游标。
```

