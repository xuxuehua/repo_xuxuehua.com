---
title: "function 函数"
date: 2018-11-21 09:06
---


[TOC]

# function



## example

### echo_color

```
#!/bin/bsh

function echo_color() {
    if [ $1 == "green" ]; then
        echo -e "\033[32;40m$2\033[0m"
    elif [ $1 == "red" ]; then
        echo -e "\033[31;40m$2\033[0m"
    fi
}

echo_color green "test_green"
echo_color red "test_red"
```





## 封装

### 提高函数复用

```
#!/bin/bash

log() { # classic logger
    local prefix="[$(date +%Y/%m/%d\ %H:%M:%S)]: "
    echo "${prefix} $@" >&2
}

log "INFO" "a message"

>>>
[2019/06/18 13:35:30]:  INFO a message
```





### 提高可读性

```
ExtractBashComments() {
    egrep "^#"
}

cat myscript.sh | ExtractBashComments | wc

comments = $(ExtractBashComments < myscript)
```

