---
title: "read"
date: 2018-09-11 23:20
---


[TOC]


# read

read 从用户输入读取 



## 定义

| read answer       | 从标准输入读取一行并赋值给变量answer                         |
| ----------------- | ------------------------------------------------------------ |
| read first last   | 从标准输入读取一行，直至遇到第一个空白符或换行符。把用户键入的第一个词存到变量first中，把该行的剩余部分保存到变量last中 |
| read              | 标准输入读取一行并赋值给内置变量REPLY                        |
| read –a arrayname | 读入一组词，依次赋值给数组arrayname                          |



## -e 启用编辑器

在交互式shell命令行中启用编辑器。例如，如果编辑器是vi，则可以在输入行时使用vi命令





## -d

 The first character of delim is used to terminate the input line, rather than newline.





## -n 不换行

参数-n的作用是不换行，echo默认是换行 

-n number 读取字符数 

read -n 5  读取5个字符输入 



## -p 输入提示

由于read命令提供了-p参数，允许在read命令行中直接指定一个提示。

-p INFO  读取用户输入 

```
#!/bin/bash 
read -p  "Enter your name: " A 
if [ "$A" = "GS" ];then 
        echo "yes" 
elif [ "$A" = "UC" ];then 
        echo "no" 
else 
        echo  "your are wrong" 
fi 
```





## -s 隐藏输入

-s INFO  隐藏用户输入，一般用户输入密钥 

```
#!/bin/bash 
read  -s  -p "Enter your password:" pass 
echo "your password is $pass" 
exit 0  
```





## -r 忽略换行符

如果带-r选项，read命令将忽略反斜杠/换行符对，而把反斜杠作为行的一部分





## -t 计时计数

-t选项指定read命令等待输入的秒数。当计时满时，read命令返回一个非零退出状态

```
#!/bin/bash 
if read -t 5 -p "please enter your name:" name 
then  
    echo "hello $name ,welcome to my script" 
else 
    echo "sorry,too slow" 
fi 
exit 0 
```



除了输入时间计时，还可以设置read命令计数输入的字符。当输入的字符数目达到预定数目时，自动退出，并将输入的数据赋值给变量。

```
#!/bin/bash 
read -n1 -p "Do you want to continue [Y/N]?" answer 
case $answer in 
Y | y) 
      echo "fine ,continue";; 
N | n) 
      echo "ok,good bye";; 
*) 
     echo "error choice";; 
esac 
exit 0 
```



该例子使用了-n选项，后接数值1，指示read命令只要接受到一个字符就退出。只要按下一个字符进行回答，read命令立即

接受输入并将其传给变量。无需按回车键



## 读文件

最后，还可以使用read命令读取Linux系统上的文件。

每次调用read命令都会读取文件中的"一行"文本。当文件没有可读的行时，read命令将以非零状态退出。

```
# !/bin/bash                       # 指定shell类型

exec 0< TimeOut.sh
count=1                            # 赋值语句，不加空格

while read line                    # read读到的值放在line中
do 
    echo "Line $count:$line"
    count=$[ $count + 1 ]          # 注意中括号中的空格
done
```





读取文件的关键是如何将文本中的数据传送给read命令。

最常用的方法是对文件使用cat命令并通过管道将结果直接传送给包含read命令的 while命令

```
# !/bin/bash                      # 指定shell类型

count=1                           # 赋值语句，不加空格

cat Timeout.sh | while read line  # cat命令查看文件Timeout.sh，然后管道给read命令，作为read的输入；read读到的值放在line中
do                                # while循环
   echo "Line $count:$line"
   count=$[ $count + 1 ]          # 注意中括号中的空格。
done
```



### while 循环



```
function while_read_LINE_bottm(){
While read LINE
do
echo $LINE
done  < $FILENAME
}
```

> 我习惯把这种方式叫做read釜底抽薪，因为这种方式在结束的时候需要执行文件，就好像是执行完的时候再把文件读进去一样。



重定向





### 重定向

```
Function While_read_LINE(){
cat $FILENAME | while read LINE
do
echo $LINE
done
}
```

> 我只所有把这种方式叫做管道法，相比大家应该可以看出来了吧。当遇见管道的时候管道左边的命令的输出会作为管道右边命令的输入然后被输入出来



### 文件描述符

```
Function while_read_line_fd(){
Exec 3<&0
Exec 0<$FILENAME
While read LINE
Do
Echo $LINE
Exec 0<&<3
}
```

> 第一，通过将所有内容重定向到文件描述符3来关闭文件描述符0.为此我们用了语法Exec 3<&0 。第二部将输入文件放送到文件描述符0，即标准输入



### for  

```
function  for_in_file(){
For  i  in  `cat $FILENAME`
do
echo $i
done
}
```

> ##### 这种方式是通过for循环的方式来读取文件的内容相比大家很熟悉了，这里不多说。