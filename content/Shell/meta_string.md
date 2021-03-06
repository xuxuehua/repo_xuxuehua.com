---
title: "meta_string 元字符"
date: 2018-10-22 12:12
---


[TOC]


# meta_string 元字符

## 特殊字符

| 字符 | 说明                                                         |
| ---- | ------------------------------------------------------------ |
| IFS  | 由\<space>或者\<tab>或者\<enter>三者之一组成，space          |
| CR   | 由\<enter>                                                   |
| =    | 设定                                                         |
| $    | 作变量或者运算替换                                           |
| >    | stdout                                                       |
| <    | stdin                                                        |
| \|   | 命令管道                                                     |
| &    | 重导向file descriptor，或将命令置于后台执行                  |
| ( )  | 将其内命令置于nested subshell执行，或用于运算和命令替换      |
| { }  | 将其内命令置于non-named function执行，或用在变量替换的界定范围 |
| ;    | 在前一个命令结束时，忽略返回值，执行下一条命了               |
| &&   | 命令返回值为true，执行下一条                                 |
| \|\| | 命令返回值为false，执行下一条                                |
| !    | 执行history命令，以数字开头                                  |



### || 或

```sh
$ cat /tmp/1.sh
particular_script()
{
    false
}

set -e

echo one
particular_script || true
echo two
particular_script
echo three

$ bash /tmp/1.sh
one
two
```

`three` will be never printed.

Also, I want to add that when `pipefail` is on, it is enough for shell to think that the entire pipe has non-zero exit code when one of commands in the pipe has non-zero exit code (with `pipefail` off it must the last one).

```shell
$ set -o pipefail
$ false | true ; echo $?
1
$ set +o pipefail
$ false | true ; echo $?
0
```





## 转义符

| 字符       | 说明                                                         |
| ---------- | ------------------------------------------------------------ |
| ' ' 单引号 | 硬转译，内部所有shell元字符，通配符都会被关掉，其硬转译内不允许出现单引号 |
| " " 双引号 | 软转译，内只允许特定的元字符，$用于参数替换,  \`用于命令替换 |
| \\ 反斜杠  | 去除气候紧跟的元字符或通配符的特殊意义                       |



### 使用`$()` 替代反引号\`

`$()` 可以支持嵌套， 而且不用转义，避免和单引号冲突

```
echo "A-`echo B-\`echo C-\\\`echo D\\\`\``"
echo "A-$(echo B-$(echo C-$(echo D)))"
>>>
A-B-C-D
A-B-C-D
```





## 使用[[ ]]代替[ ]



```
[ "${name}" \> "a" -o "${name}" \< "m" ]

[[ "${name}" > "a" && "${name}" < "m" ]]
```



[[ ]]更符合人性编码, 避免转义问题

有不少新功能, 包含但不限于：

|| ：逻辑or

&& ：逻辑and

< ：字符串比较（不需要转义）

== ：通配符(globbing)字符串比较

=~ ：正则表达式(regular expression, RegEx)字符串比较



需要注意的是，从bash3.2开始，通配符和正则表达式都不能用引号包裹了

```
t="abc123"
[[ "$t" == abc* ]] 	#true	globbing比较
[[ "$t" == "abc*" ]]	#false 字面比较
[[ "$t" =~ [abc]+[123]+ ]]	#true 	#正则表达式比较
[[ "$t" =~ "abc*" ]]	#false	#字面比较
```

> 这里加了引号就是字面比较