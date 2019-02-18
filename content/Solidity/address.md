---
title: "address"
date: 2018-08-12 19:53
---

[TOC]

# address 类型



## address.transfer(value)

调用后，可以将异常返回到调用者手中

建议使用在转钱的过程



## address.send(value)

只会返回布尔类型，不会有具体的报错信息



## address.call, address.callcode, address.delegatecall

常用于智能合约和智能合约之间相互调用彼此函数的时候



## 0x0

While the zero address is only intended for contract registration, it sometimes receives payments from various addresses. There are two explanations for this:

- either it is by accident, resulting in the loss of ether

- or it is an intentional ether burn (see [burning_ether]).

  > If you want to do an intentional ether burn, you should make your intention clear to the network and use the specially designated burn address instead