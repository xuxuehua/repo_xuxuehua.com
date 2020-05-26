---
title: "set"
date: 2018-11-22 11:16
---


[TOC]


# set



## -e

causes the shell to exit if any subcommand or pipeline returns a non-zero status.

The answer the interviewer was probably looking for is:

```
It would be dangerous to use "set -e" when creating init.d scripts:
```

你写的每个脚本都应该在文件开头加上`set -e`, 这句语句告诉bash如果任何语句的执行结果不是`true`则应该退出. 这样的好处是防止错误像滚雪球般变大导致一个致命的错误, 而这些错误本应该在之前就被处理掉. 如果要增加可读性, 可以使用`set -o errexit`, 它的作用与`set -e`相同



## +o 

To permanently disable shell command history

```
echo 'set +o history' >> ~/.bashrc
```



disable a command history system wide

```
# echo 'set +o history' >> /etc/profile
```









## -o 

### -o errexit

遇到执行错误，会抛出并终止





### -o pipefail

希望在执行错误之后立即退出, 不要再向下执行了. 而 `-o pipefail` 的作用域是管道, 也就是说在 Linux 脚本中的管道, 如果前面的命令执行出了问题, 应该立即退出



```
$ vim set_o_pipefail.sh
$ cat set_o_pipefail.sh
#!/bin/bash
set -o pipefail
ls ./a.txt |echo "hi" >/dev/null
echo $?
$
$ bash set_o_pipefail.sh
ls: cannot access ./a.txt: No such file or directory
2
$
$ vim set_o_pipefail.sh
$ cat set_o_pipefail.sh
#!/bin/bash
#set -o pipefail
ls ./a.txt |echo "hi" >/dev/null
echo $?
$
$ bash set_o_pipefail.sh
ls: cannot access ./a.txt: No such file or directory
0
$
```





### -o nounset

抛出默认不存在的变量错误 





### -o verbose

永久指定输出调试信息



### -o xtrace

永久指定输出调试信息



## -u

不存在的变量会报错，并立即停止

```
$ cat 1.sh 
#!/bin/bash
set -u

cd $a/*
echo "hello world"


$ bash 1.sh 
1.sh: line 4: a: unbound variable
```



## -x 记录历史

记录操作历史