---
title: "sed"
date: 2018-09-27 16:01
---


[TOC]


# sed

sed [-nefri] 'command' 输入文本 



## -i 插入

### 删除

```
sed -i '6d' ~/.ssh/known_hosts
```



### 替换

```
sed -i 'columns/.*/replacement-word/' file.txt
```

```
sed -i 's/some_a/some_b/g' file.txt
```

