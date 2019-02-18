---
title: "sar"
date: 2018-11-27 01:07
---


[TOC]


# sar

sar 命令用于收集、汇报和保存系统活动信息





## -n 网络统计

```
# sar -n DEV | more
```



显示 24 日的网络统计

```
# sar -n DEV -f /var/log/sa/sa24 | more
```



显示实时使用情况

```
# sar 4 5
```

