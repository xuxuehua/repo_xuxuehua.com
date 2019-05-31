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



用命令行往文件的顶部添加文字

```
sed -i '1s/^/line to insert\n/' path/to/file/you/want/to/change.txt
```



用命令行往配置文件里插入多行文本

```
cat >> path/to/file/to/append-to.txt << "EOF"
export PATH=$HOME/jdk1.8.0_31/bin:$PATH
export JAVA_HOME=$HOME/jdk1.8.0_31/
EOF
```







### 替换

```
sed -i 'columns/.*/replacement-word/' file.txt
```

```
sed -i 's/some_a/some_b/g' file.txt
```

