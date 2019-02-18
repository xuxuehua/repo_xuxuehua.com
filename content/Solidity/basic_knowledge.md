---
title: "basic_knowledge"
date: 2018-08-12 18:59
---

[TOC]

# solidity

## Solidity 工具列表

- - [Dapp](https://dapp.readthedocs.io/)

    Solidity 语言的构建工具、包管理器以及部署助手。

- - [Solidity REPL](https://github.com/raineorshine/solidity-repl)

    一个命令行控制台，可以让你立刻尝试 Solidity 语言。

- - [solgraph](https://github.com/raineorshine/solgraph)

    可视化的 Solidity 控制流，并能标明潜在的安全漏洞。

- - [evmdis](https://github.com/Arachnid/evmdis)

    EVM 反汇编程序，可以执行字节码的静态分析，能提供比 EVM 操作更高级的抽象。

- - [Doxity](https://github.com/DigixGlobal/doxity)

    Solidity 语言的文档生成器。



## 第三方 Solidity 解析器和语法

- - [solidity-parser](https://github.com/ConsenSys/solidity-parser)

    JavaScript 的 Solidity 解析器

- - [Solidity Grammar for ANTLR 4](https://github.com/federicobond/solidity-antlr4)

    ANTLR 4 解析器生成器的 Solidity 语法





## Hello World 代码

```
pragma solidity ^0.4.21; // 程序版本

contract SimpleStorage {       // 合约声明, 封装了合约的方法和变量
    uint storedData;    //  声明一个类型为 uint (256位无符号整数）的状态变量

    function set(uint x) {      // 合约的方法
        storedData = x;
    }

    function get() constant returns (uint){     
        return storedData;
    }
}
```

>  constant（视觉上的警示） -> view （最新版本变成view 方法）
>
>  Return uint 指定返回类型
>
>  该合约能完成的事情并不多（由于以太坊构建的基础架构的原因）：它能允许任何人在合约中存储一个单独的数字，并且这个数字可以被世界上任何人访问，且没有可行的办法阻止你发布这个数字。当然，任何人都可以再次调用 `set` ，传入不同的值，覆盖你的数字，但是这个数字仍会被存储在区块链的历史记录中。随后，我们会看到怎样施加访问限制，以确保只有你才能改变这个数字。





## pure

比constant警示作用更严格，函数中，成员变量无法读写

pure修饰的函数不能改也不能读状态变量，只能操作函数内部变量，否则编译通不过。

## view

view的作用和constant一模一样，可以读取状态变量但是不能改




## remix 在线编辑器

[remix.ethereum.org](remix.ethereum.org) 

## eth gas

在函数调用的通过的gas 来完成

 

## 全局变量



### ether

ether单位

- wei
- szabo = 10^12 wei
- finney = 10^15 wei
- ether = 10^18 wei



### 时间单位

- seconds
- minutes
- hours
- days
- weeks
- years



### **block块**：

> 像singleton，整个程序里面只有一个，谁都能access

- block.blockhash(uint blockNumber) returns (bytes32)
- block.coinbase(address)：谁挖到了这个block
- block.difficulty(uint) 难度
- block.gaslimit(uint)
- block.number(uint)：这是第几个block
- block.timestamp(uint)：block目前创建的时间
- now：相当于block.timestamp的快捷方式



### **msg消息**

包含智能合约中函数调用者的信息和数据

- msg.data：包含函数调用所有的raw信息，如名字等
- msg.gas(uint)：查看函数调用者附带了多少gas
- msg.sender(address)：谁调用了这个函数
- msg.sig
- msg.value(uint)：以wei为单位的msg中带有的钱

### 

 ## 局部变量

`solidity`中变量的作用域类似`javascript`，与`java`不同：只要在`function`中定义了局部变量，这个局部变量的作用域会穿透`{}`，在整个`function`中都有效

