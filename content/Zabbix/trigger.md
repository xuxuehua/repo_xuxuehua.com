---
title: "trigger"
date: 2019-03-07 18:05
---


[TOC]



# Trigger

zabbix server 每次接收到items 的新数据时，就会对Item的当前采样值进行判断，即和trigger表达式进行比较





一个Trigger 只能属于一个item

但一个item可以有多个trigger



## 状态



### OK



### PROBLEM

有事件发生



## Severity



### Not Classified 

未知级别

灰色



### Information 

一般信息

亮绿



### Warning

警告信息 

黄色



### Average

一般故障

橙色



### High

高级别故障

红色



### Disaster

致命故障

亮红









## 触发条件



### Trigger events

OK --> PROBLEM



### Discovery events

zabbix的network discovery工作时的发现机制



### Auto registration events

主动模式的agent注册时产生的事件



### Internal events

Item变成不再被支持，或Trigger 变成未知状态











