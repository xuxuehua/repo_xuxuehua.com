---
title: "event_scheduler 事件调度器"
date: 2018-12-23 19:38
---


[TOC]



# Event Scheduler

事件调度器(Event Scheduler)是在MySQL`v5.1.6`中新增的一个功能，它相当于一个定时器，可以在指定的时间点执行一条SQL语句或一个语句块，也可以用于在固定间隔重复执行。事件调度器相当于操作系统中的定时任务(如：Linux中的cron、Window中的计划任务)，但MySql的事件调度器可以精确到秒，对于一些实时性要求较高的数据处理非常有用。



## 调度器的配置

### 调度器状态

要保证创建的事件能正常执行，首先应该开启事件调度器，可以通过以下3种方式查看调度器状态：

```
SHOW VARIABLES LIKE 'event_scheduler';
SELECT @@event_scheduler;
SHOW PROCESSLIST;
```

查看某个事件的执行情况：

```
SELECT * FROM information_schema.EVENTS;
```

以上会输出当关Schema中所有的事件信息，可以先通过`DESC information_schema.EVENTS;`查看输出字段，再查看所需要的信息。如，我只想看事件名及最后执行时间：

```
SELECT EVENT_NAME, LAST_EXECUTED FROM information_schema.EVENTS;
```



### 开启/关闭事件调度器

如果事件调度器未开启，可以通过以下4种方式启用：

```
SET GLOBAL event_scheduler = 1;
SET @@global.event_scheduler = 1;
SET GLOBAL event_scheduler = ON;
SET @@global.event_scheduler = ON;
```

`1`或`ON`表示设置为开启状态。同样的，如果需要关闭只要将值`0`或`OFF`即可。



## 创建事件

在MySql中，创建一个新的调度器使用`CREATE EVENT`，其语法规则如下：

```
CREATE
    [DEFINER = { user | CURRENT_USER }]
    EVENT
    [IF NOT EXISTS]
    event_name
    ON SCHEDULE schedule
    [ON COMPLETION [NOT] PRESERVE]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'comment']
    DO event_body;
```

> event_name: 事件名 可以是任何合的MySql标识符，不能超64个字符。
>
> 创建事件时，可以同时指定Schema，语法结构为：`schema_name.event_name`
>
> schedule: 调度规则，规定事件的执行时间与执行规则。是一个可包含以下值的子语句：

```
schedule:
    AT timestamp [+ INTERVAL interval] ...
  | EVERY interval
    [STARTS timestamp [+ INTERVAL interval] ...]
    [ENDS timestamp [+ INTERVAL interval] ...]

interval:
    quantity {YEAR | QUARTER | MONTH | DAY | HOUR | MINUTE |
              WEEK | SECOND | YEAR_MONTH | DAY_HOUR | DAY_MINUTE |
              DAY_SECOND | HOUR_MINUTE | HOUR_SECOND | MINUTE_SECOND}
```

>  `event_body` - 事件体，可以是单行SQL语法，或是`BEGIN……END`语句块



## 修改事件

对于已存在事件调度器，可以使用`ALTER`语句进行修改，语法结构如下：

```
ALTER
    [DEFINER = { user | CURRENT_USER }]
    EVENT event_name
    [ON SCHEDULE schedule]
    [ON COMPLETION [NOT] PRESERVE]
    [RENAME TO new_event_name]
    [ENABLE | DISABLE | DISABLE ON SLAVE]
    [COMMENT 'comment']
    [DO event_body]
```

事件的开启与关闭本质是使用`ALTER`语句修改已创建的事件。如，关闭一个事件：

```
ALTER EVENT e_test ON COMPLETION PRESERVE ENABLE;
```

开启一个事件：

```
ALTER EVENT e_test ON COMPLETION PRESERVE DISABLE;
```





## example

### 定时增加

一个最简单的示例，将`myschema.mytable`表的`mycol`列，每小时自增`1`：

```
CREATE EVENT myevent
    ON SCHEDULE 
    AT CURRENT_TIMESTAMP + INTERVAL 1 HOUR
    DO
      UPDATE myschema.mytable SET mycol = mycol + 1;
```

这样，我们就创建一个名为`myevent`的事件，它会在事件创建后每小时执行一次。设置的执行规则等价于：

```
CREATE EVENT myevent
    ON SCHEDULE 
    EVERY 1 HOUR
    STARTS CURRENT_TIMESTAMP
    DO
      UPDATE myschema.mytable SET mycol = mycol + 1;
```

如果需要间隔一定时间再开启事务，如，1天后开启：

```
CREATE EVENT myevent
    ON SCHEDULE 
    EVERY 1 HOUR
    STARTS CURRENT_TIMESTAMP + INTERVAL 1 DAY
    DO
      UPDATE myschema.mytable SET mycol = mycol + 1;
```

`DO`执行的SQL可以是一个语句块，如：

```
DELIMITER //  
CREATE EVENT e  
ON SCHEDULE  
    EVERY 5 SECOND  
DO  
BEGIN  
    DECLARE v INTEGER;  
    DECLARE CONTINUE HANDLER FOR SQLEXCEPTION BEGIN END;  
    SET v = 0;  
    WHILE v < 5 DO  
        INSERT INTO t1 VALUES (0);      
        UPDATE t2 SET s1 = s1 + 1;  
        SET v = v + 1;  
    END WHILE;  
END //  
DELIMITER ;  
```





### 定时插入

使用event来执行存储过程，定时统计数据，插入到一张统计数据表中

```
CREATE DEFINER=`root`@`localhost` EVENT `event_timer_toaddreport` ON SCHEDULE EVERY 1 DAY STARTS '2017-07-21 02:00:00' ON COMPLETION PRESERVE ENABLE DO CALL sp_addreport()
```

//注释：以上时间触发器，从2017年7月21日凌晨2点开始，每天的凌晨2点都会执行 Call sp_addreport()这个存储过程，可在这个存储过程中编写添加，编辑，删除等SQL语句。