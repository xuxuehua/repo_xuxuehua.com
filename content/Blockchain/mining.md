---
title: "mining"
date: 2021-03-19 19:22
---
[toc]



# Mining 挖矿

“挖矿”只是一种类比，与黄金的挖掘方式相类比，黄金的开采费时费力，而且黄金资源有限。比特币的挖取也是费时（需要大量的哈希计算）、费电（一台专业矿机每天要消耗100多度电），而且BTC资源也有限，总量2100万个，每四年新币数量减半，越挖越少



比特币的总量有限，只有2100万个。2100万只是一个容易让人记住的数字，实际上准确数字应该是20999999.9769个，比2100万少一点点。

比特币的发行量有一个重要的规则，每产生21万个区块（大约四年），每个区块中奖励的币数减半，预计在2140年，比特币会达到极限值2100万个，不再增长了。





挖矿任务的实施者叫“矿工(Miner)”，不像挖黄金里的矿工，这里的矿工是一台冰冷的计算机，通常叫做矿机，它们配有专业的挖矿芯片及挖矿软件，每天要消耗巨大的电力来支撑其复杂的计算



挖矿有两个意义：一是验证交易的合法性，写入大账本；二是发行新币。由于这个行业的巨大经济诱惑，随着时间推移，大量的计算机投入到这种计算中，通常的CPU被高性能的GPU显卡取代，再后来，专用的挖矿芯片ASIC问世，运算效率是CPU计算的上万倍。如果你现在想用自己的台式机挖矿，就相当于你用一双手挖黄金，而别人用专业团队+全副武装的挖掘设备来挖，你可能忙活几百年也挖不到1个币





## PoW

PoW是Proof of Work的缩写，即工作量证明的意思。为了解决拜占庭将军问题，即在不信任的去中心化的网络中，如何确认一笔交易的有效性？

比特币系统中引入了“工作量”的概念，有意降低了信息传递的效率，让矿工必须完成一定的工作量，才能够在全网广播消息。



工作量证明可以与工地搬砖进行类比。一群工人们（矿工）向火车的车皮（区块）里搬砖，每个工人身边都有一个空的集装箱，这个集装箱与火车车皮一样大，正好能够装满1000块砖。

工人们只能往集装箱里搬砖，谁先装满集装箱，就把这个集装箱放到车皮里，领取6.25元的工钱（实际上并不是马上拿走，100节车皮之后才能真正取走）。只有最快装满集装箱的工人能够获得奖励，在这个集装箱放入车皮的同时，其他工人的集装箱里也装了一些砖头了，竞争失败意味着全部作废，倒出来重新搬砖，继续投入到下一节车皮的竞争中。

比特币世界里的矿工（工人）也是这样辛苦，这里的矿工是一堆安装了专门芯片的电脑，它们的工作就是进行哈希HASH计算（准确讲是SHA256，高级搬砖工作），谁先算完，写入一个新区块（车皮），得到奖励的6.25个BTC（发行新币，前面讲过还有交易费的奖励），其它矿工则白忙活，继续进行下一个区块的竞争。

因为电脑的计算速度太快，所以要安排上亿次的HASH计算，保证有矿工在10分钟左右能够完成任务。这种工作量，既是一种发行货币的过程，也是一种验证其他人交易的过程，从而保证了整个比特币系统的安全性。







## Pool

单个矿工的力量毕竟有限，它们则采用集团作战的方式，组成“矿池(Pool)”，每个矿工按贡献率分成。


