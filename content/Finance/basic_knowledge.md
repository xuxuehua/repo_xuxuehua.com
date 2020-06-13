---
title: "basic_knowledge"
date: 2020-04-13 19:58
---
[toc]





# 期权 Option

金融衍生品的一种，不是证券(security)

即简单又复杂



常见的金融衍生品，期货（future，forward） ，对赌协议（swap）



一种权利，花钱买期权





## 看涨期权 Call Option

在未来一段时间内，以一个现在就约定好的特定价格，去买一个标的物的权利

To long(write/wrote) a call 去买一个看涨期权



## 看跌期权 Put Option

在未来一段时间内，以一个现在就约定好的特定价格，去卖一个标的物的权利

To short(write/wrote) a put  去卖一个看跌期权





## 过期日 Expiration Date 

未来一段时间的截止



## 行权价 Execise Price(Strike Price)

固定的价格去买卖



## 标的物 Underlining

可以是股票，可以是债券，还可以是其他的金融衍生品



# 期权分类

在其他相同的条件下，美式期权比欧式期权贵



## America Option 

在一段时间内可以去买卖标的物，行权时间可以在任意期间内





## European Option

只能在最后一天行权





# 期权判断

判断对了很难， 股票而言，只需要判断对了方向即可

做空，不但要判断对方向，还要判断对时间

若认为下周跌，做空的话，下个月跌，那期间的issue和margin call（追缴保证金）就可能撑不下去了



期权而言，要判读好

1. 大方向
2. 涨或者跌的幅度
3. 时间



# 看涨期权案例

买期权的人，最大的损失（即最小的收益）是期权本身的价格成本。最大的收益是无穷大

而卖期权的人，正好相反，最大的收益是期权本身的价格，最大的损失也是无穷大

## 情况1

若买一个期权，标的物为一支股票，到期日是6个月之内，即6个月内都可以行权。行权价是50元，其市场价在20-30之间浮动，股票价格20

6月之内没有超过50元，期权就白买了，即没有涨到50元的行权价，买到的权利没有用，期权就自动过期，最大的损失就是购买期权

## 情况2

若涨到50元，但其实没有用，即可以行使权力卖掉，当然，在open market 也可以以50元的价格买到，没有优惠

## 情况3

在6个月内，涨超过50元，如60元，可以在open market 上卖出，每一股获利10元，但一定是在这个时间范围内










