---
title: "pymysql"
date: 2018-09-26 14:01
---


[TOC]


# pymysql



## 安装

```
pip install pymysql
```



## 初始化连接

```
conn = pymysql.connect(host='localhost', user='root', password='password', database='demo', port=3306)
cursor = conn.cursor()



conn.commit()
conn.close()
```



## 事物的标准操作

事务机制可以确保数据的一致性，也就是这件事要么发生了，要么没有发生。比如插入一条数据，不会存在插入一半的情况，要么全部插入，要么都不插入，这就是事务的原子性。另外，事务还有3个属性——一致性、隔离性和持久性。这4个属性通常称为ACID特性



| 属性                  | 解释                                                         |
| --------------------- | ------------------------------------------------------------ |
| 原子性（atomicity）   | 事务是一个不可分割的工作单位，事务中包括的诸操作要么都做，要么都不做 |
| 一致性（consistency） | 事务必须使数据库从一个一致性状态变到另一个一致性状态。一致性与原子性是密切相关的 |
| 隔离性（isolation）   | 一个事务的执行不能被其他事务干扰，即一个事务内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰 |
| 持久性（durability）  | 持续性也称永久性（permanence），指一个事务一旦提交，它对数据库中数据的改变就应该是永久性的。接下来的其他操作或故障不应该对其有任何影响 |



插入、更新和删除操作都是对数据库进行更改的操作，而更改操作都必须为一个事务，所以这些操作的标准写法就是

```
try:
    cursor.execute(sql)
    db.commit()
except:
    db.rollback()
```

> 这里的`commit()`和`rollback()`方法就为事务的实现提供了支持。



这样便可以保证数据的一致性。这里的`commit()`和`rollback()`方法就为事务的实现提供了支持。





## 插入数据

### 单条数据

```
sql = '''
insert into user(id, username, age, password) values(1, 'aaa', 18, '123456')
'''

cursor.execute(sql)
conn.commit()
```

> 需要执行`db`对象的`commit()`方法才可实现数据插入，这个方法才是真正将语句提交到数据库执行的方法。对于数据插入、更新、删除操作，都需要调用该方法才能生效。



### 多条数据

```
sql = '''
insert into user(id, username, age, password) values(njull, %s, %s, %s)
'''
username = 'aaa'
age = 18
password = '123456'

cursor.execute(sql, (username, age, password))
conn.commit()
```



### 字典方法

```
data = {
    'id': '20120001',
    'name': 'Bob',
    'age': 20
}
table = 'students'
keys = ', '.join(data.keys())
values = ', '.join(['%s'] * len(data))
sql = 'INSERT INTO {table}({keys}) VALUES ({values})'.format(table=table, keys=keys, values=values)
try:
   if cursor.execute(sql, tuple(data.values())):
       print('Successful')
       db.commit()
except:
    print('Failed')
    db.rollback()
db.close()
```

> SQL语句`INSERT INTO students(id, name, age) VALUES (%s, %s, %s)`



## 查询数据

### fetchone()

获取结果的第一条数据，返回结果是元组形式，元组的元素顺序跟字段一一对应



* while 方法

```
sql = 'SELECT * FROM students WHERE age >= 20'
try:
    cursor.execute(sql)
    print('Count:', cursor.rowcount)
    row = cursor.fetchone()
    while row:
        print('Row:', row)
        row = cursor.fetchone()
except:
    print('Error')
```





```
sql = 'SELECT * FROM students WHERE age >= 20'
 
try:
    cursor.execute(sql)
    print('Count:', cursor.rowcount)
    one = cursor.fetchone()
    print('One:', one)
    results = cursor.fetchall()
    print('Results:', results)
    print('Results Type:', type(results))
    for row in results:
        print(row)
except:
    print('Error')
```





### fetchall()

`fetchall()`方法，它可以得到结果的所有数据。然后将其结果和类型打印出来，它是二重元组，每个元素都是一条记录

```
cursor = conn.cursor()

sql = '''
select * from user
'''

cursor.execute(sql)
results = cursor.fetchall()
for result in results:
    print(result)
conn.close()
```



```
sql = 'SELECT * FROM students WHERE age >= 20'
 
try:
    cursor.execute(sql)
    print('Count:', cursor.rowcount)
    one = cursor.fetchone()
    print('One:', one)
    results = cursor.fetchall()
    print('Results:', results)
    print('Results Type:', type(results))
    for row in results:
        print(row)
except:
    print('Error')
```



### fetchmany(size)

获取指定条数的数据

```
cursor = conn.cursor()

sql = '''
select * from user
'''

cursor.execute(sql)
results = cursor.fetchmany(3)
for result in results:
    print(result)
conn.close()
```



### SSCursor 处理大结果集的方法

`PyMySQL`在获取数据时提供了`fetchone()`和`fetchall()`函数来获取结果集，后来调试的时候，发现，不管是哪种方法，都会一次将所有结果获取到，这在数据量很大时将会消耗大量内存，所以有考虑是否还有别的方法，比如`one-by-one`的迭代获取。



在查看`DictCursor`游标代码时，发现了`SSCursor`游标类，其注释如下，意在解决数据量大的问题

```
class SSCursor(Cursor):
    """
    Unbuffered Cursor, mainly useful for queries that return a lot of data,
    or for connections to remote servers over a slow network.

    Instead of copying every row of data into a buffer, this will fetch
    rows as needed. The upside of this, is the client uses much less memory,
    and rows are returned much faster when traveling over a slow network,
    or if the result set is very big.

    There are limitations, though. The MySQL protocol doesn't support
    returning the total number of rows, so the only way to tell how many rows
    there are is to iterate over every row returned. Also, it currently isn't
    possible to scroll backwards, as only the current row is held in memory.
    """
```

`DictCursor`游标类的方法返回都是一个迭代器，可以使用这个迭代器进行迭代获取，这样就不用一次将所有数据保存在内存中了。

```
def fetchall_unbuffered(self):
    """
    Fetch all, implemented as a generator, which isn't to standard,
    however, it doesn't make sense to return everything in a list, as that
    would use ridiculous memory for large result sets.
    """
    return iter(self.fetchone, None)
```

使用方法如下：

```
import pymysql.cursors
src_pc_database = pymysql.connect(host='192.168.39.51', port=5151, user='*', password='*',
                               db='testdataanalyse',
                               charset='utf8mb4', cursorclass=pymysql.cursors.SSDictCursor)

with src_pc_database.cursor() as src_cursor:
    sql = "select * from user"
    src_cursor.execute(sql)
    result = src_cursor.fetchone()
    
    while result is not None:
        result = src_cursor.fetchone()

src_pc_database.close()
```





## 删除数据

### 单条数据

```
cursor = conn.cursor()

sql = '''
delete from user where id=1
'''

cursor.execute(sql)
conn.commit()
conn.close()
```



### 灵活删除

```
table = 'students'
condition = 'age > 20'

sql = 'DELETE FROM  {table} WHERE {condition}'.format(table=table, condition=condition)
try:
    cursor.execute(sql)
    db.commit()
except:
    db.rollback()

db.close()
```



## 更新数据

### 单条数据

```
cursor = conn.cursor()

sql = '''
update user set username='aaa' where id=1
'''

cursor.execute(sql)
conn.commit()
conn.close()
```



### 灵活更新	

```
data = {
    'id': '20120001',
    'name': 'bob',
    'age': 21
}

table = 'students'
keys = ', '.join(data.keys())
values = ', '.join(['%s'] * len(data))

sql = "INSERT INTO {table}({keys}) VALUES ({values}) ON DUPLICATE KEY UPDATE".format(table=table, keys=keys, values=values)
update = ','.join([' {key} = %s'.format(key=key) for key in data])
sql += update

try:
    if cursor.execute(sql, tuple(data.values())*2):
        print('Successful')
        db.commit()
except:
    print('Failed.')
    db.rollback()

db.close()
```

> 其sql语句为`INSERT INTO students(id, name, age) VALUES (%s, %s, %s) ON DUPLICATE KEY UPDATE id = %s, name = %s, age = %s`
>
> 这里就变成了6个`%s`。所以在后面的`execute()`方法的第二个参数元组就需要乘以2变成原来的2倍





