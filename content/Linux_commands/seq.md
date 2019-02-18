---
title: "seq"
date: 2018-07-16 21:05
---

[TOC]



# seq



## -f



```
ADS_NYC=$(seq -f SDNYads%03g 101 200)
```



```
!513 $ for i in `seq -f  nycelkes%02g 1 10`; do echo $i; done
nycelkes01
nycelkes02
nycelkes03
nycelkes04
nycelkes05
nycelkes06
nycelkes07
nycelkes08
nycelkes09
nycelkes10
```

