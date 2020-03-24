---
title: "circulation"
date: 2018-09-05 01:01
---

[TOC]

# 循环





## if

```
if commands; then
    commands
[elif commands; then
    commands...]
[else
    commands]
fi
```

```
FILE=~/.zshrc # 随便找个路径
if [ -e "$FILE" ]; then # -e 单目操作符
    if [ -f "$FILE" ]; then
        echo "$FILE is a regular file."
    fi
    if [ -d "$FILE" ]; then
        echo "$FILE is a directory."
    fi
    if [ -r "$FILE" ]; then
        echo "$FILE is readable."
    fi
    if [ -w "$FILE" ]; then
        echo "$FILE is writable."
    fi
    if [ -x "$FILE" ]; then
        echo "$FILE is executable/searchable."
    fi
else
    echo "$FILE does not exist"
fi
```



## case

`case` 其实就是我们熟悉的那个 `switch` ，但语法形式上有很大的不同。

```
case "$variable" in
    "$condition1" )
        command...
    ;;
    "$condition2" )
        command...
    ;;
esac
```

- 双引号包裹变量，这不是必须的
- 每一个 `Test` 语句，必须以 `)` 结尾
- 每一个条件区块，必须以 `;;` 结尾
- 整个 `case` 区块，必须以 `esac` 结尾——`esac case spelled backwards`

来个例子。

```
x=4

case $x in
    'a' )
        echo "x 是 a";;
    4 )
        echo "x 是 4";;
    'b' )
        echo "x 是 b"
esac

# x 是 4
```



## for

```javascript
for variable [in words]; do
    commands
done
```

- `do` 可以另起一行，如果和 `for` 同行，那么 `for` 语句必须 `;` 结尾
- 循环体必须 `done` 结尾
- `[in words]` 取值很宽泛，可以是通配符，可以是一个命令(`ls`)，一句话，必须是数组形式

```
for i in *
do
    echo $i;
done

## 会打印当前目录下的所有文件名
```



```
for i in {1..5}
do 
echo $i
done

>>>
1
2
3
4
5
```

```
for FILE in $HOME/.bash*
do 
echo $FILE
done

>>>
/Users/xhxu/.bash_history
/Users/xhxu/.bash_profile
/Users/xhxu/.bash_sessions
```



## while

```
count=1
while [ $count -le 5 ]; do
    echo $count
    count=$((count + 1))
done
echo "Finished."

# 依次打印 1 - 5 和 finished
```

语法如下：

```
while commands; do commands; done
```



### read 读取

读取每一行内容

```
$ cat 1 | while read line ; do echo $line ; done
1
2
3
4
5
```





## break 

跳出所有循环



### break n 

跳出第n层循环



## continue

跳出当前循环



