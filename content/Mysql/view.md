---
title: "view 视图"
date: 2020-03-22 11:37
---
[toc]



# 视图

将一段查询sql封装为一个虚拟的表。 相当于是一张表或多张表的数据结果集

这个虚拟表只保存了sql逻辑，不会保存任何查询结果。





## 特点

在大型项目中，以及数据表比较复杂的情况下，视图的价值就凸显出来了，它可以帮助我们把经常查询的结果集放到虚拟表中，提升使用效率

视图提供了一个统一访问数据的接口。（即可以允许用户通过视图访问数据的安全机制，而不授予用户直接访问底层表的权限），从而加强了安全性，使用户只能看到视图所显示的数据



mysql的视图中不允许有from 后面的子查询，但oracle可以





## 嵌套视图

创建好一张视图之后，还可以在它的基础上继续创建视图

```
CREATE VIEW player_above_above_avg_height AS
SELECT player_id, height
FROM player
WHERE height > (SELECT AVG(height) from player_above_avg_height);
```



# 使用

## 创建

```
CREATE VIEW view_name  
AS 
SELECT column_name(s) 
FROM table_name 
WHERE condition 
```



```
CREATE VIEW player_above_avg_height AS
SELECT player_id, height
FROM player
WHERE height > (SELECT AVG(height) from player);
```



## 查询

```
select * from view_name;
```

```
SELECT * FROM player_above_avg_height;
```



## 更新

```
CREATE OR REPLACE VIEW view_name  
AS 
SELECT column_name(s) 
FROM table_name 
WHERE condition 
```



## 修改

```
ALTER VIEW view_name AS
SELECT column1, column2
FROM table
WHERE condition
```



```
ALTER VIEW player_above_avg_height AS
SELECT player_id, player_name, height
FROM player
WHERE height > (SELECT AVG(height) from player);
```





## 删除

```
DROP VIEW [IF EXISTS]
    view_name [, view_name] ...
    [RESTRICT | CASCADE]
```







