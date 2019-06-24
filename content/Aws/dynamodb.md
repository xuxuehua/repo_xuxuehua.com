---
title: "dynamodb"
date: 2019-06-20 17:35
---
[TOC]

# DynamoDB

文档和键值对nosql，适合广告，游戏，IOT



限制在单独的AWS region

3个独立的location 复制数据（同一个表到多个region）



## Characteristic

Store any amount of data with no limits

Fast, predictable performance using SSDs

Easily provision and change the request capacity needed for each table

Fully managed, NoSQL database service



## Data Model

![img](https://snag.gy/mLOVXZ.jpg)



Table is collection of items

items is collection of attributes

Each attribute is name-value pairs, which contains single values, json or set values.



### Primary keys

Primary keys is unique identity of each items in the table

![img](https://snag.gy/M1ISyA.jpg)



### Local Secondary Index

![img](https://snag.gy/R30Mm1.jpg)



### Global Secondary Index

![img](https://snag.gy/xkaIhq.jpg)











