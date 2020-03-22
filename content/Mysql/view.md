---
title: "view 视图"
date: 2020-03-22 11:37
---
[toc]



# 视图

将一段查询sql封装为一个虚拟的表。 

这个虚拟表只保存了sql逻辑，不会保存任何查询结果。



## 特点

封装复杂sql语句，提高复用性

逻辑放在数据库上面，更新不需要发布程序，面对频繁的需求变更更灵活



mysql的视图中不允许有from 后面的子查询，但oracle可以

## 

# 使用

## 创建

```
CREATE VIEW view_name  
AS 
SELECT column_name(s) 
FROM table_name 
WHERE condition 
```





## 查询

```
select * from view_name  
```



## 更新

```
CREATE OR REPLACE VIEW view_name  
AS 
SELECT column_name(s) 
FROM table_name 
WHERE condition 

```

