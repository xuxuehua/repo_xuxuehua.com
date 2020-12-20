---
title: "basic_knowledge"
date: 2020-06-26 02:29
---
[toc]





# ORM

![image-20200626102526967](basic_knowledge.assets/image-20200626102526967.png)



Object Relation Mapping，中文叫对象关系映射。它是 RDBMS 和业务实体对象之间的一个映射，从图中你也能看到，它可以把底层的 RDBMS 封装成业务实体对象，提供给业务逻辑层使用

hibernate:强调对单条数据的处理
mybits:基于自定义配置的sql操作



## 优缺点

优点：
安全：因为屏蔽了数据库的具体操作细节以及对sql做了严格的过滤，因此能够保证数据库信息的隐蔽性，同时防止sql注入。
简单：屏蔽了数据层的访问细节，我们只需要集中注意力处理业务逻辑就可以了。

缺点：
性能低：自动化意味着加载很多即使没有必要的关联和映射，牺牲性能。但ORM也采取了一些补救措施：对象懒加载，缓存技术等。





# SQLAlchemy



## Hello world

```
# -*- coding:utf-8 -*-
from sqlalchemy import and_
from sqlalchemy import Column, INT, FLOAT, VARCHAR
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from sqlalchemy.ext.declarative import declarative_base
Base = declarative_base()


class Test_db:
    def __init__(self):
        """此处填上自己的连接配置"""
        self.engine = create_engine(
            'mysql+pymysql://username:password@host:port/db_name?charset=utf8')
        db_session = sessionmaker(bind=self.engine)
        self.session = db_session()

    def query_all(self, target_class, query_filter):
        result_list = self.session.query(target_class).filter(query_filter).all()
        self.session.close()
        return result_list


class Heros(Base):
    """定义表结构"""
    __tablename__ = 'heros'
    id = Column(INT(), primary_key=True)
    name = Column(VARCHAR(255))
    hp_max = Column(FLOAT())
    mp_max = Column(FLOAT())

    def __init__(self, id, name, hp_max, mp_max):
        self.id = id
        self.name = name
        self.hp_max = hp_max
        self.mp_max = mp_max


if __name__ == '__main__':
    db_obj = Test_db()
    query_filter = and_(Heros.hp_max > 6000)
    heros = db_obj.query_all(Heros, query_filter)
    for hero_info in heros:
        print("id:{}, name:{}, hp_max:{}, mp_max:{}".format(hero_info.id, hero_info.name,
                                                            hero_info.hp_max, hero_info.mp_max))
```







# SQLAlchemy functions



## one_or_none()

Return at most one result or raise an exception.

Returns `None` if the query selects no rows. 

Raises `sqlalchemy.orm.exc.MultipleResultsFound` if multiple object identities are returned, or if multiple rows are returned for a query that returns only scalar values as opposed to full identity-mapped entities.





## not_()  / ~ 

Return a negation of the given clause, i.e. `NOT(clause)`.


The `~` operator is also overloaded on all ColumnElement subclasses to produce the same result.

When used with SQL expressions, results in a NOT operation, equivalent to [`not_()`](https://docs.sqlalchemy.org/en/13/core/sqlelement.html#sqlalchemy.sql.expression.not_), that is:

```
~a
```

is equivalent to:

```
from sqlalchemy import not_
not_(a)
```





## first()

`Query.first()` returns the first of a potentially larger result set (adding `LIMIT 1` to the query), or `None` if there were no results. No exception will be raised.

