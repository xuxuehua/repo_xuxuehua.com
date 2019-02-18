---
title: "payment_system"
date: 2018-08-12 19:59
---

[TOC]



# 员工支付系统



## 设计

* 薪水将全部基于以太币

uint salary
address frank



* 每30天发放一次

uint payDuration
uint lastPayday


如何发放薪水

- 定时器？：**只有被动调用的方式**

  > 在ethereum区块里面，每一个区块，会执行一个完整的函数，所以不具备定时器的功能

- 设计范式

  > 通过合约做可信中介，月初时老董先存够30天工钱，frank开始干活，30天结束后才可以从合约上取钱

- 在合约上存钱，frank在每30天领取薪水

  - function addFund
  - function calculateRunway：获取余额还能支付几次
  - function hasEnoughFund
  - function getPaid