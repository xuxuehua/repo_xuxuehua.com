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

