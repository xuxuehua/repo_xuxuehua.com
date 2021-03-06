---
title: "mvcc"
date: 2020-06-22 23:52
---
[toc]





# MVCC

Multiversion Concurrency Control

Mvcc 可以不采用锁机制，而是通过乐观锁的方式来解决不可重复读和幻读问题

多版本并发控制技术。从名字中也能看出来，MVCC 是通过数据行的多个版本管理来实现数据库的并发控制，简单来说它的思想就是保存数据的历史版本。这样我们就可以通过比较版本号决定数据是否显示出来，读取数据的时候不需要加锁也可以保证事务的隔离效果。



## 特点

MVCC 我们可以解决以下几个问题：

读写之间阻塞的问题，通过 MVCC 可以让读写互相不阻塞，即读不阻塞写，写不阻塞读，这样就可以提升事务并发处理能力。

降低了死锁的概率。这是因为 MVCC 采用了乐观锁的方式，读取数据时并不需要加锁，对于写操作，也只锁定必要的行。

解决一致性读的问题。一致性读也被称为快照读，当我们查询数据库在某个时间点的快照时，只能看到这个时间点之前事务提交更新的结果，而不能看到这个时间点之后事务提交的更新结果。

