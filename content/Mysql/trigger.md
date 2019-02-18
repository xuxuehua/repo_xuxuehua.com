---
title: "trigger 触发器"
date: 2018-12-23 19:47
---


[TOC]



# trigger 触发器

MySQL中，触发器是一种与数据表事件相关的特殊形式的存储过程，是建在表上的命名数据库对象，触发器常用于加强数据的完整性约束和复杂的业务规则等。



## 特点

- 建在表上：触发器定义在特定的数据表上，这个表可叫做触发器表。触发器可以查询其他表，引用其他表的字段，可以包含复杂的SQL语句。触发器所在表必须为永久性表，不能将触发程序与TEMPORARY表或视图关联起来。
- 由事件触发：不同于存储过程，触发器不能手动调用，也不能接收、传送参数。必须对表上INSERT、UPDATE 或 DELETE 操作定义了触发器，然后对应地执行 INSERT、UPDATE 或 DELETE 操作时，该触发器才可被激活，触发程序自动执行。
- 可调用存储过程：为响应数据库的变化，触发器可以调用一个或多个存储过程，保证数据完整性、一致性。



## 创建触发器

### 语法

```
CREATE TRIGGER trigger_name trigger_time trigger_event
ON tbl_name FOR EACH ROW trigger_stmt
```

> trigger_name:触发器名称，可以自己定义
>
> trigger_time:触发时机，有两个取值BEFORE|AFTER，指定触发器执行的时间。
>
> trigger_event:触发事件，取值包括INSERT UPDATE和DELETE。
>
> tbl_name:建立触发器的数据表的名称，即在哪张表上建立触发器。
>
> FOR EACH ROW：表示任何一条满足触发条件的记录上的操作都会触发触发器。
>
> trigger_stmt:触发器程序体，指触发器被触发后执行的程序，可以是一条SQL语句，也可以是BEGIN和END包含的多条语句。



### trigger_time

需要注意的是不能在同一张表上建立2种同类型的触发器，一张表最多创建6个触发器(6种类型)：即BEFORE INSERT、BEFORE UPDATE、BEFORE DELETE、AFTER INSERT、AFTER UPDATE、AFTER DELETE。



### trigger_event

trigger_event可以是下述值之一：

INSERT：将新行插入表时激活触发程序，例如，通过INSERT、LOAD DATA和REPLACE语句。

UPDATE：更改某一行时激活触发程序，例如，通过UPDATE语句。

DELETE：从表中删除某一行时激活触发程序，例如，通过DELETE和REPLACE语句。

请注意，trigger_event与以表操作方式激活触发程序的SQL语句并不很类似，这点很重要。

对于某一表，不能有两个BEFORE UPDATE触发程序。但可以有1个BEFORE UPDATE触发程序和1个BEFOREINSERT触发程序，或1个BEFORE UPDATE触发程序和1个AFTER UPDATE触发程序。





## 删除触发器

```
DROP TRIGGER [IF EXISTS] [schema_name.]trigger_name
```







## 触发事件

MySQL除了对insert update delete基本操作进行定义外，还定义了load data和replace语句，这两种语句也能触发以上6种类型的触发器的触发。

LOAD DATA 语句将一个文件装入到一个数据表中，相当与是一系列的 INSERT 操作。

replace和insert语句也很像，只是在表中有 primary key 或 unique 索引时，如果插入的数据和原来 primary key 或 unique 索引一致时，会先删除原来的数据，然后增加一条新数据，也就是说，一条 REPLACE 语句有时候等价于一条。



### INSERT

触发器插入某一行时激活触发器，可能通过 INSERT、LOAD DATA、REPLACE 语句触发；

在INSERT触发程序中，仅能使用NEW.col_name，没有旧行



### UPDATE

更改某一行时激活触发器，可能通过 UPDATE 语句触发；

在UPDATE触发程序中，可以使用OLD.col_name来引用更新前的某一记录行的列，也可使用NEW.col_name来引用更新后的记录行中的列。

用OLD命名的列是只读的。你可以引用它，但不能更改它。对于用NEW命名的列，如果具有SELECT权限，可引用它。在BEFORE触发程序中，如果你具有UPDATE权限，可使用“SET NEW.col_name = value”更改它的值。这意味着，你可以使用触发程序来更改将要插入到新行中的值，或用于更新行的值。



### DELETE

删除某一行时激活触发器，可能通过 DELETE、REPLACE 语句触发。

在DELETE触发程序中，仅能使用OLD.col_name，没有新行。





## BEGIN … END 语句

语句格式

```
BEGIN   statement...list   END
```

statement_list 代表一个或多个语句的列表，列表内的每条语句都必须用分号（;）来结尾。

在BEGIN…END块中，还能使用存储过程的其他语法，如条件判断和循环语句。此时，需要重新定义SQL语句分隔符，以便能够在触发程序中使用SQL结束符“;”。

```
mysql> delimiter $$;
```

> 定义$$ 为结束符号

```
create trigger tr_insertEmp after insert
on employees for each row
begin
	insert salaries values(new.emp_no,0,'2018-12-23','2018-12-23');
end;
$$;
```





### BEFORE

在BEFORE触发程序中，AUTO_INCREMENT列的NEW值为0，不是实际插入新记录时将自动生成的序列号。



## MySQL 触发器在操作事务性和非事务性表的区别

对于事务性表，触发器在执行过程中若有一个触发程序执行失败，那么整个触发程序将回滚。对于非事务性表，如果后面部分语句执行失败，但在这之前执行的语句依然有效，无法撤销。

在触发器执行过程中，MySQL的错误处理机制如下：

1. 如果BEFORE型触发器执行失败，相应行的操作也不会被执行。
2. BEFORE型触发器是有对行的插入或修改行为激活，无论后续的插入或修改是否成功。
3. 只有当所有的BEFORE型触发器和所有的行操作全部执行成功，AFTER型触发器才被执行。
4. 如果BEFORE或AFTER触发器在执行过程中出现错误，将导致调用触发器的整个SQL语句执行失败。
5. 对于事务性表，触发器SQL语句如果执行失败，那么由此执行引起的所有改变都将回滚。

MySQL数据库引擎默认为MyISAM，不支持事务和外键，InnoDB可支持事务和外键。



# example



