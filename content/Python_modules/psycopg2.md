---
title: "psycopg2"
date: 2020-09-16 01:13
---
[toc]





# psycopg2



## installation

```
pip install psycopg2-binary
```





# Hello World

```
import psycopg2


def make_connection(user, password, host, port, database):
    conn = psycopg2.connect(
        user=user,
        password=password,
        host=host,
        port=port,
        database=database
    )
    conn.autocommit = True
    return conn

user='postgres'
password='abc123'
host='127.0.0.1'
port=55433
database='postgres'

connection = make_connection(user=user, password=password, host=host, port=port, database=database)
cursor = connection.cursor()

cursor.execute()
cursor.fetchall()
```





# cursor



## .mogrify

for multiple insert into

```
data = [(1,2),(3,4)]
args_str = ','.join(['%s'] * len(data))
sql = "insert into t (a, b) values {}".format(args_str)

print(cursor.mogrify(sql, data).decode('utf8'))

>>>
insert into t (a, b) values (1, 2),(3, 4)
```



```
data = [('new_type_string1', ), ('new_type_string2', )]
args_str = ','.join(['%s'] * len(data))
sql = "insert into app4 () values {}".format(args_str)
print (cursor.mogrify(sql, data).decode('utf8'))

>>>
insert into app4 () values ('new_type_string1'),('new_type_string2')
```

