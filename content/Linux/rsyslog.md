---
title: "rsyslog"
date: 2018-12-18 01:32
---


[TOC]



# Rsyslog



rsyslog记录着系统中的任何事件，管理者可以通过查看系统记录随时掌握系统状况。系统日志通过rsyslog进程记录系统的有关事件，也可以记录应用程序运作事件。通过适当配置，还可以实现运行rsyslog协议的机器之间的通信。通过分析这些网络行为日志，可追踪和掌握与设备和网络有关的情况。



rsyslog日志消息既可以记录在本地文件中，也可以通过网络发送到接收rsyslog的服务器。



## rsyslog日志系统

在Rsyslog系统有两个进程分别是klogd,syslogd



### klogd

记录内核信息，系统启动中在登录之前使用的都是物理终端/dev/console，这个时候虚拟终端还没有启动而内核启动日志都存放在/var/log/dmesg文件中，使用dmesg命令可以查看。



### syslogd

记录非内核系统产生的信息，当系统启动/sbin/init程序时产生的日志都存放在以下各个日志文件中。

```
/var/log/message    #标准系统错误信息；
/var/log/maillog    #邮件系统产生的日志信息；
/var/log/secure     #记录系统的登录情况；
/var/log/dmesg      #记录linux系统在引导过程中的各种记录信息；
/var/log/cron       #记录crond计划任务产生的时间信息；
/var/log/lastlog    #记录每个用户最近的登录事件；
/var/log/wtmp       #记录每个用户登录、注销及系统启动和停机事件；
/var/run/btmp       #记录失败的、错误的登录尝试及验证事件；
```



#### syslog协议格式

```
facility.priority action
```



#### facility（设施）

标识系统需要记录日志的子系统

```
auth             #PAM认证相关日志；
authpriv         #SSH、FTP登录相关日志；
cron             #任务计划相关日志；
daemon           #守护进程相关日志；
kern             #内核相关日志；
lpr              #打印相关日志；
mail             #邮件相关日志；
mark             #标记相关日志；
news             #新闻相关日志；
security         #安全相关日志与auth类似；
syslog           #Rsyslog自己的日志；
user             #用户相关日志；
uucp             #UNIX to UNIX cp相关日志；
local0 to local7 #用户自定义使用设置日志；
*                #表示所有的facility；
```



#### priority（级别）

用来标识日志级别,级别越低信息越详细，有以下日志级别，从上到下，级别从低到高，记录的信息越来越少，详细的可以查看手册: man 3 syslog。

```
debug            #程序或系统调试信息； 详细
info             #一般信息；
notice           #不影响正常功能需要注意的信息；
warning          #可能影响系统功能提醒用户的重要事件；
error            #错误信息；
crit             #比较严重的信息；
alert            #必须马上处理的警告信息；
emerg/panic      #会导致系统不可用的严重信息；不详细
*                #表示所有日志级别；
none             #跟*相反表示什么都没有；
```



#### action（动作）

设置日志记录的位置，有以下几种。

* 记录到普通文件或设备文件

```
*.*     /var/log/file.log   #绝对路径
*.*     /dev/pts/0          #设备文件
```

* ”|”，表示将日志送给其他命令处理



* ”@HOST”，表示将日志发送到特定的主机

```
*.emerg                     @192.168.10.1
```



* ”用户”，表示将日志发送到特定的用户



* ”*”，表示将日志发送所有登录到系统上的用户



* 连接符号

```
.                  #表示大于等于xxx级别的信息；
.=                 #表示等于xxx级别的信息；
.!                 #表示在xxx之外的等级的信息；
```



##### 格式定义案例

（定义在/etc/rsyslog.conf配置文件）

```
# 表示将mail相关的,info级别及以上级别都记录到mail.log文件中
mail.info  /var/log/mail.log
 
# 表示将auth相关的基本为info信息记录到远程主机
auth.=info @192.168.10.1
 
# 表示记录与user和error相反的
user.!error
 
# 表示记录所有日志信息的info级别及以上级别
*.info
 
# 所有日志及所有级别信息都记录下来
*.*
```

> 多个日志来源可以使用,号隔开,如cron.info;mail.info。







## 属性替代

Rsyslog预定义了一些属性，来代表消息中的信息，我们可以在定义输出格式、动态文件名的时候使用到这些属性。这里面比较重要的属性比如：msg（消息体）、hostname、pri（消息等级和类别）、time（时间有关），属性的名称中以`$`开头的是从本地系统获得的变量、不带`$`是从消息中获得变量。



### 语法格式

```
%propname:fromChar:toChar:options:fieldname%
```



属性替换的功能很强大，你可以使用起始字符获取自己所需的字段，也可以使用正则表达式，也可以使用分隔符。举几个例子：为了兼容一个rfc协议，rsyslog规定如果用户输入的msg不是以空格开头，rsyslog会自动补充一个空格，因此如果你希望输出的时候去掉这个空格，就可以使用

```
%msg:2:$%     #选取msg变量中，起始位置为2，终止位置为结尾
```



我们经常需要根据空格来分析字符串，F表示使用字符分割，32是空格的ascii码，例：

```
%msg:F,32:3%  #按照空格分隔，取第三个子串，
```



## 模板

模板的功能是定义输出格式，或者定义omfile模块的动态路径、动态文件。需要使用上面提到的属性替换。

模板定义的形式有四种，适用于不同的输出模块，一般简单的格式，可以使用string的形式，复杂的格式，建议使用list的形式，使用list的形式，可以使用一些额外的属性字段（property statement），例如：position.from、position.end。



如果不指定输出模板，rsyslog会默认使用RSYSLOG_DEFAULT。

如果你只想输出msg，可以定义模板:

```
$template  t_msg, "%msg\n%"
```



如果想按日期保存输出，需要使用动态路径。可以定义模板

```
$template  f_debug, "/data0/logs/%$year%-%$month%-%$day%/debug.log"
```





## Ruleset

Ruleset实现的是多实例的功能，可以针对syslog的来源使用不同的过滤规则。需要注意的是，在配置文件中需要先定义ruleset，才可以使用。比较典型的一个例子，针对不同的端口使用不同的过滤规则。



```
$Ruleset tcp1999
$RulesetCreateMainQueue on
Local3.*      @@10.0.0.44:1999
$Ruleset tcp2000
$RulesetCreateMainQueue on
Local4.*      @@10.0.0.44:2000
```

> 在定义好ruleset后，各个输出模块就可以指定自己使用的ruleset了，具体如何指定，可以查看输出模块的手册，一般会有一个ruleset的参数，用来实现这个功能。



## Filter模块

Rsyslog可以使用syslog标准的过滤规则，同时自己添加了一些扩展。比如可以在输出中指定rsyslog自己的处理方式，可以指定输出template,方法是在规则后面添加template的名字，用分号隔开。



例如我们可以编写一个规则:

```

```



```
Local3.*    -/data0/logs/local3.log;t_msg   #在这个输出中使用t_msg的模板
Local4.*    -?f_local3_test;t_msg           #问号表示要使用模板定义的动态路径
除了syslog标准的规则，rsyslog的作者还自己开发了一个叫做rainerscript的脚本语言，来定义更复杂的过滤过则，rainerscript可以对属性进行startwith、contains、%（取余）等过滤规则，例如:


If $pri-txt == local3.* and $msg contains “abc” then{  #pri为local3，且在消息中包含子串‘abc’
       *.*   -/data0/logs/local3.log;t_msg
}
1
2
3
If $pri-txt == local3.* and $msg contains “abc” then{  #pri为local3，且在消息中包含子串‘abc’
       *.*   -/data0/logs/local3.log;t_msg
}
还有第三种方式是使用属性的表示方式，例如:


:msg, regex, "^ [g-z]"    /root/rsyslog_worker_dir/2000.log   #以字母g到z开头的消息，注意msg开头有个空格
1
:msg, regex, "^ [g-z]"    /root/rsyslog_worker_dir/2000.log   #以字母g到z开头的消息，注意msg开头有个空格
5）队列

队列是rsyslog中比较重要的一个部分，作为使用者，我们需要了解的是队列的种类：主队列和工作队列。从输入模块接收的消息会进入主队列，主队列中的消息，经过过滤模块，会进入到相应的工作队列；队列的四种工作模式：direct mode、disk mode、FixedArray mode和LinkedList mode，前两种是磁盘队列，更可靠，但是性能也较差，后两种是内存队列，区别是前者是预分配队列长度，后者是动态分配，如果你的系统日志流量比较平稳，可以使用预分配队列，如果日志属于突发型，可以使用动态队列。此外，内存队列还可以通过指定一个queuename来添加DA模式，DA模式主要是为了防止意外情况（进程关闭、server端宕机）下，内存队列可以不丢失。

通过查看rsyslog的系统命令，可以知道rsyslog对队列进行大量的可配参数，来定义队列的行为。可以根据需要来进行优化。
```















#### 日志切割

另外当日志文件过大时会通过系统crontab定义日志滚动的，也称日志切割，由logrotate控制其配置文件/etc/logrotate.conf，然后再由系统任务计划执行。logrotate负责备份和删除旧日志, 以及更新日志文件。







