---
title: "iptables_usage"
date: 2018-11-27 18:44
---


[TOC]





# iptables 基本语法

```bash
iptables [-t table] Sub-commands Chain [num] Creatria -j Target
```



## -t table

用来指明使用的表，有三种选项: filter，nat，mangle，raw

若未指定，则默认使用filter表





## Sub-commands 对链的操作

以下这些规则参数用于描述数据包的协议、源地址、目的地址、允许经过的网络接口，以及如何处理这些数据包







### 链管理

#### -E 重命名链

rename，重命名自定义的链，引用计数不为0的自定义链，无法改名，也无法删除



#### -F 清空规则

flush，清空指定表的指定链上所有规则；省略链名时，清空表中的所有链



#### -N 自定义链

new，新建一条自定义的规则链；被内建链上的规则调用才能生效；[-j  chain_name]；自定义链只能作为默认链上的跳转对象，即在默认链通过引用来生效自定义链



#### -P 默认策略

policy，设置链的默认处理机制；当所有都无法匹配或有匹配有无法做出有效处理机制时，默认策略即生效；

filter表的可用策略：ACCEPT, DROP, REJECT



#### -X 删除空链

drop，删除用户自定义的空链；非空自定义链和内置链无法删除；



#### -Z 重置计数

zero, 将规则的计数器置0 



### 规则管理

#### -A 追加新规则

append 追加，在指定链的尾部追加一条规则

```
iptables -A chain firewall-rule
```

> -A chain – 指定要追加规则的链
>
> firewall-rule – 具体的规则参数



#### -I 插入规则

insert 在指定的位置（省略位置时表示链首）插入一条规则；



#### -D 删除规则

delelte 删除指定链上的指定规则



#### -R 替换规则

replace， 将指定的规则替换为新规则；不能仅修改规则中的部分，而是整条规则完全替换；





#### -L 查看规则

list，列出表中的链上的所有规则



##### -n 显示IP及端口

numeric，以数值格式显示输出中的IP地址和端口号，而不是默认的名字，比如主机名、网络名、程序名等，即不反解



##### -v verbose/ -vv / -vvv

verbose，显示规则的详细格式信息，包括规则计数器等





##### -x 显示准确数值

exactly，使--list输出中的计数器显示准确的数值，而不用K、M、G等估值；



##### --line-numbers 序号

该选项的作用是显示出每条规则在相应链中的序号，对插入新规则很有用；



##### --modprobe 模块

此选项告诉iptables探测并装载要使用的模块。这是非常有用的一个选项，若modprobe命令不在搜索路径中，就要用到了。有了这个选项， 在装载模块时，即使有一个需要用到的模块没装载上，iptables也知道要去搜索。







## Createria 匹配条件

多重条件：逻辑关系为“与”；匹配选项指定数据包与规则匹配所具有的特征，包括源地址，目的地址，传输协议和端口号



### 基本匹配条件

#### [!] -p/–protocol 协议

检查报文中的协议，即ip首部中的protocols所标识的协议，protocol

如tcp, udp, icmp等，可以使用all来指定所有协议。

如果不指定-p参数，则默认是all值。这并不明智，请总是明确指定协议名称。

可以使用协议名(如tcp)，或者是协议值（比如6代表tcp）来指定协议。映射关系请查看/etc/protocols



#### [!] -s/-src/-source 源地址

检查报文中的源IP地址是否符合此处指定的地址或范围

指定数据包的源地址

参数可以使IP地址、网络地址、主机名

例如：-s 192.168.1.101指定IP地址

例如：-s 192.168.1.10/24指定网络地址

如果不指定-s参数，就代表所有地址





#### [!] -d/-dst/-destination 目的地址

检查报文中的目标IP地址是否符合此处指定的地址或范围

参数和-s相同





#### [!] -i/-in-interface 输入接口

数据报文的流入接口；通常只用于INPUT, FORWARD 和 PREROUTING 链上的规则

-i代表输入接口(input interface)

-i指定了要处理来自哪个接口的数据包

例如：-i eth0指定了要处理经由eth0进入的数据包

如果不指定-i参数，那么将处理进入所有接口的数据包

如果出现! -i eth0，那么将处理所有经由eth0以外的接口进入的数据包 

如果出现-i eth+，那么将处理所有经由eth开头的接口进入的数据包

```
iptables -A INPUT -d 172.16.0.1 -i eth0 -j ACCEPT
```





#### [!] -o/-out-interface 输出接口

数据报文的流出接口；通常只用于 FORWARD, OUTPUT 和 POSTROUTING链上的规则；

-o指定了数据包由哪个接口输出

如果不指定-o选项，那么系统上的所有接口都可以作为输出接口

如果出现! -o eth0，那么将从eth0以外的接口输出 

如果出现-i eth+，那么将仅从eth开头的接口输出

```
iptables -A OUTPUT -s 172.16.0.1 -o eth0 -J ACCEPT
```







### 扩展匹配条件

使用 iptables 的模块实现扩展性检查机制，扩展匹配又分为隐式扩展和显式扩展

```
-m match_name --spec_options
```



#### 隐式扩展

如果在通用匹配上使用 -p 选项指明了协议的话，就无需使用-m指定扩展模块

##### -p tcp

```bash
-p tcp：隐含了-m tcp：针对 tcp 协议
   [!] --source-port,--sport port[:port]：匹配报文中传输层的源端口；
   [!] --destination-port,--dport port[:port]：匹配报文中传输层的目标端口；
   [!] --tcp-flags L1 L2
       SYN，ACK，FIN，RST，URG，PSH；    
       检查L1所指明的所有标志位，且这其中L2表示所有标记为必须为1，而余下的必须为0。没有L1指明的，不做检查
      
   [!] --syn：
       相当于--tcp-flags  SYN,ACK,FIN,RST  SYN (检查SYN，ACK，FIN，RST， 只能SYN为1， 即三次握手的第一次)
```



##### -p udp


```
-p udp：隐含了-m udp：针对 udp 协议
    [!] --source-port,--sport port[:port]：匹配报文中传输层的源端口；
    [!] --destination-port,--dport port[:port]：匹配报文中传输层的目标端口；
```



##### -p icmp

```
-p icmp：隐含了-m icmp：针对 icmp 协议
    [!] --icmp-type {type[/code]|typename}
        8：echo-request
        0：echo-reply
```

```
iptables -A OUTPUT -s 172.16.0.9 -p icmp --icmp-type 8 -J ACCEPT
iptables -A INPUT -d 172.16.0.9 -p icmp --icmp-type 0 -J ACCEPT
```



 





#### 显式扩展

必须使用-m选项指出match name使用的扩展机制，有的match可能存在专用的选项

```bash
 -m 模块名称
```

> 每个模块会引入新的匹配机制；可以通过 rpm -ql iptables 来获取哪些模块可用，模块是以 .so 结尾的
>
> CentOS 7 $ man iptables-extensions



##### multiport 

多端口匹配

以离散或连续的方式定义多端口匹配条件；最多指定15个端口；

```bash
[!] --source-ports,--sports port[,port|,port:port]...：指定多个源端口；
[!] --destination-ports,--dports port[,port|,port:port]...：指定多个目标端口；
[!] --ports port[,port|,port:port]...：指定多个端口；
```

```
iptables -I INPUT -s 172.16.0.0/16 -d 172.16.0.9 -p tcp -m multiport --dports 22,80 -j ACCEPT
iptables -I OUTPUT -d 172.16.0.0/16 -s 172.16.0.9 -p tcp -m multiport --sports 22,80 -j ACCEPT
```





允许172.16.100.1-100对172.16.100.11的telnet23端口访问

```
# iptables -A INPUT -d 172.16.100.11 -p tcp --dport 23 -miprange --src-range172.16.100.1-172.16.100.100 -j ACCEPT
# iptables -A OUTPUT -s 172.16.100.11 -p tcp --sport 23-m iprange --dst-range 172.16.100.1-172.16.100.100 -j ACCEPT
```



##### icmp 

icmp-type 8 请求

icmp-type 0 应答

基于发包速率作限制；

```bash
        --limit n[/second|/minit|/hour|/day]
        --limit-burst n
```


##### iprange

以连续的ip地址范围, 指明连续的多地址匹配条件

```bash
[!] --src-range from[-to]：源IP地址范围
[!] --dst-range from[-to]：目标IP地址范围
```

```
iptables -I INPUT -d 172.16.0.9 -p tcp -m multiport --dports 22:23,80 -m iprange --src-range 172.16.100.1-172.16.100.100 -j ACCEPT
iptables -I OUTPUT -s 172.16.0.9 -p tcp -m multiport --sports 22:23,80 -m iprange --drc-range 172.16.100.1-172.16.100.100 -j ACCEPT
```



##### set

依赖于ipset命令行工具



##### string

检查报文中出现的字符串，对报文中的应用层数据做字符串匹配检测

字符串匹配检查算法：kmp，bm

```
--algo {bm|kmp}  字符串比对算法 （必选）
[!] --string pattern：要检测字符串模式；
[!] --hex-string pattern：要检测的字符串模式，16进制编码；
```



检查出口报文中含有movie的数据包

```
iptables -I OUTPUT -m string --algo bm --string 'movie' -j REJECT 
```



##### time

根据报文到达的时间与指定的时间范围进行匹配度检测

```
--datestart YYYY[-MM[-DD[Thh[:mm[:ss]]]]]：起始日期时间；
--datestop YYYY[-MM[-DD[Thh[:mm[:ss]]]]]：结束日期时间；
--timestart hh:mm[:ss]
--timestop  hh:mm[:ss]
[!] --monthdays day[,day...]
[!] --weekdays day[,day...]
--kerneltz：使用内核中配置的时
```



时间为UTC时间

```
iptables -I INPUT -d 172.16.0.9 -p tcp --dport 80 -m time --timestart 14:00 --timestop 16:00 -j REJECT
```



在周1,2,4,5的08:30-18:30拒绝172.16.100.11对80端口的访问

```
# iptables -A INPUT -d 172.16.100.11 -p tcp --dport 80 -m time --timestart 08:30 --timestop 18:30 --weekdays Mon,Tue,Thu,Fri -j REJECT
```



##### connlimit

基于连接数作限制，对每个IP能够发起的并发连接数作限制；根据每客户端IP做并发连接数匹配；

```bash
--connlimit-upto n：连接数数量小于等于n，此时应该允许；
--connlimit-above n：连接数数量大于n，此时应该拒绝；
--connlimit-mask prefix length：
--connlimit-saddr：
--connlimit-daddr：
```

```
iptables -I INPUT -p tcp --dport 22 -m connlimit --connlimt-above 3 -j REJECT
```



当连接172.16.100.11的ssh数大于5时[包括5个]拒绝

```
# iptables -I INPUT 2 -d 172.16.100.11 -p tcp --dport 22 -m connlimit --connlimit-above 5 -j REJECT
# iptables -P INPUT ACCEPT
```



配置本机的telnet服务，要求只允许来自于172.16.0.0/16网络中的主机访问，且只允许工作时间访问，而且，每个来源IP最多的并发连接数不能超过2个；

```
方法1：
# iptables -A INPUT -s 172.16.0.0/16-d 172.16.37.10 -p tcp --dport 23 -m time --timestart 01:00 --timestop 20:00 -mconnlimit ! --connlimit-above 2 -j ACCEPT
# iptables -P DROP

方法2：先拒绝在允许
# iptables -I INPUT 1 -p tcp -dport 23 -m connlimit --connlimit-above 2 -jDROP
# iptables -I INPUT 2 -p tcp -dport 23 -j ACCEPT
```



##### limit

基于发包速率作限制

专用选项：令牌桶算法

```
--limit  n[/second|/minit|/hour|/day]   
--limit-burst n    峰值为几，即最大突发为几
```

```
--limit 10/minit 指明每分钟允许10个数据包
```



```
iptables -A INPUT -d 172.16.0.1 -p icmp --icmp-type 8 -m limit --limit 30/minute -j ACCEPT
```





```
# iptables -A INPUT -p icmp --icmp-type 8 -m limit --limit 6/m --limit-burst 5 -j ACCEPT
# iptables -P INPUT DROP
```

> 首先我们能看到前四个包的回应都非常正常，然后从第五个包开始，我们每10秒能收到一个正常的回应。这是因为我们设定了单位时间(在这里是每分钟)内允许通过的数据包的个数是每分钟6个，也即每10秒钟一个；其次我们又设定了事件触发阀值为5，所以我们的前四个包都是正常的，只是从第五个包开始，限制规则开始生效，故只能每10秒收到一个正常回应。
>
> 假设我们停止ping，30秒后又开始ping，这时的现象是：
>
> 前两个包是正常的，从第三个包开始丢包，这是因为在这里我的允许一个包通过的周期是10秒，如果在一个周期内系统没有收到符合条件的包，系统的触发值就会恢复1，所以如果我们30秒内没有符合条件的包通过，系统的触发值就会恢复到3，如果5个周期内都没有符合条件的包通过，系统都触发值就会完全恢复。



##### state

状态检测；连接追踪机制（conntrack）；

调整连接追踪功能能够容纳的最大连接数量（需要单独提高）

```
/proc/sys/net/nf_conntrack_max 最大追踪的链接
```

已经追踪到并记录下的追踪信息

```
/proc/net/nf_conntrack
```

不同协议或者连接类型的时长

```
/proc/sys/net/netfilter/
```







启用连接追踪模板记录连接，并根据连接匹配连接状态的扩展；

连接追踪模板，用于记录各连接及相关状态；基于IP实现，与是否为TCP协议无关；通过倒计时的方式删除条目；记录连接的状态有

```
NEW：新发出的请求，连接追踪模版中不存在此连接相关的信息条目，因此，将其识别为第一次发出的请求 
ESTABLISHED：NEW 状态之后，连接追踪模板中为其建立的条目在失效之前期间内所进行的通信状态
RELATED：相关的连接，如ftp协议的命令连接与数据连接即为相关联的连接；
UNTRACKED：未追踪的连接；
INVALID：无法识别的状态； 
```

```bash
[!] --state STATE
```





放行icmp，22， 80 连接

```
iptables -I INPUT -d 172.16.0.1 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -I OUTPUT -s 172.16.0.1 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT
```

```
iptables -I INPUT -d 172.16.0.1 -p tcp --dport 80 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -I OUTPUT -s 172.16.0.1 -p tcp --sport 80 -m state --state ESTABLISHED -j ACCEPT
```

```
iptables -I INPUT -d 172.16.0.1 -p icmp --icmp-type 8 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -I OUTPUT -s 172.16.0.1 -p icmp --icmp-type 0 -m state --state ESBTALISHED -j ACCEPT
```



规则优化，即已连接的数据包进行放行

```
iptables -I INPUT -m state --state ESTABLISHED -m ACCEPT
iptables -I OUTPUT -m state --state ESTABLISHED -j ACCEPT
```

单独对22，80等端口的建立进行检查

```
iptables -I INPUT 2 -d 172.16.0.1 -p tcp -m multiport --dports 22,80 -m state --state NEW -j ACCEPT
```





开放被动模式的ftp服务

1. 装载ftp专用模块

```
# ls -l /lib/modules/4.15.1-1.el7.elrepo.x86_64/kernel/net/netfilter/nf_conntrack_ftp.ko
-rwxr--r--. 1 root root 27472 Feb  4  2018 /lib/modules/4.15.1-1.el7.elrepo.x86_64/kernel/net/netfilter/nf_conntrack_ftp.ko

# modinfo nf_conntrack_ftp.ko
filename:       /lib/modules/4.15.1-1.el7.elrepo.x86_64/kernel/net/netfilter/nf_conntrack_ftp.ko
alias:          nfct-helper-ftp
alias:          ip_conntrack_ftp
description:    ftp connection tracking helper
author:         Rusty Russell <rusty@rustcorp.com.au>
license:        GPL
srcversion:     F7866B81ED3FB7B77825505
depends:        nf_conntrack
intree:         Y
name:           nf_conntrack_ftp
vermagic:       4.15.1-1.el7.elrepo.x86_64 SMP mod_unload modversions
parm:           ports:array of ushort
parm:           loose:bool

# modprobe nf_conntrack_ftp

#  lsmod | grep ftp
nf_conntrack_ftp       20480  0
nf_conntrack          135168  9 nf_conntrack_ipv6,nf_conntrack_ftp,nf_conntrack_ipv4,ipt_MASQUERADE,nf_nat_ipv6,nf_nat_masquerade_ipv4,xt_conntrack,nf_nat_ipv4,nf_nat

```



2. 放行请求报文

命令连接： NEW ESTABLISHED

数据连接：RELATED  ESTABLISHED

```
iptables -A INPUT -d local_ip -p tcp --dport 21 -m state --state NEW,ESTABLISHED -m ACCEPT
iptables -A INPUT -d local_ip -p tcp -m state --state RELATED,ESTABLISHED -m ACCEPT
```



3. 放行响应报文

```
iptables -A OUTPUT -s local_ip -p tcp -m state --state ESTABLISHED -j ACCEPT
```









## Target 执行目标

-j代表”jump to target”

-j指定了当与规则(Rule)匹配时如何处理数据包

可能的值是ACCEPT, DROP, QUEUE, RETURN

还可以指定其他链（Chain）作为目标





## Target 采取的动作

target/jump决定符合条件的包到何处去，



target的目标是具体的操作

target指定要对包做的操作，比如DROP和ACCEPT。不同的target有不同的结果。一些target会使包停止前进，也就是不再继续比较当前链中的其他规则或父链中的其他规则。而另外一些target在对包做完操作之后，包还会继续和其他的规则比较，如LOG，ULOG和TOS。它们会对包进行记录，然后让包通过，以便匹配这条链中的其他规则。有了这样的target，就可以对同一个包既改变它的TTL又改变它的TOS。有些target必须要有准确的参数（如TOS需要确定的数值），有些就不是必须的，但如果我们想指定也可以（如日志的前缀，伪装使用的端口，等等）。



### ACCEPT

接受；当包满足了指定的匹配条件，就会被ACCEPT，允许包前往下一个目的地。不会再去匹配当前链中的其他规则或同一个表内的其他规则，但包还要通过其他表中的链，可能会被DROP。





### DROP

丢弃；当信息包与具有DROP目标的规则完全匹配时，会阻塞该信息包，并且不对它做进一步处理。该目标被指定为-j DROP。若包符合条件，该target就会将target丢掉，也就是说包的生命到此结束，效果就是包被阻塞了。在某些情况下，这个target会引起意外的结果，因为它不会向发送者返回任何信 息，也不会向路由器返回信息，这就可能会使连接的另一方的sockets因苦等回音而亡:) 解决这个问题的较 好的办法是使用REJECT target，（译者注：因为它在丢弃包的同时还会向发送者返 回一个错误信息，这样另一方就能正常结束），尤其是在阻止端口扫描工具获得更多的信息时，可以隐蔽被 过滤掉的端口等等（译者注：因为扫描工具扫描一个端口时，如果没有返回信息，一般会认为端口未打开或 被防火墙等设备过滤掉了）。还要注意如果包在子链中被DROP了，那么它在主链里也不会再继续前进，不管 是在当前的表还是在其他表里。总之，包死翘翘了。







### REJECT

拒绝；REJECT和DROP基本一样，区别在于它除了阻塞包之外， 还向发送者返回错误信息。target还只能用在INPUT、FORWARD、OUTPUT和它们的子链里，而且包含 REJECT的链也只能被它们调用，否则不能发挥作用。它只有一个选项，是用来控制 返回的错误信息的种类的。虽然有很多种类，但如果你有TCP/IP方面的基础知识，就很容易理解它们。拦阻该数据包，并返回数据包通知对方，可以返回的数据包有几个选择：ICMP port-unreachable、ICMP echo-reply 或是tcp-reset（这个数据包包会要求对方关闭联机），进行完此处理动作后，将不再比对其它规则，直接中断过滤程序。



以上连接追踪功能内核会在内存中开辟一段专用的空间用于存储连接的状态等，由于此段内存空间是有限的，因此就必须对连接追踪功能进行一些调整限制，有关参数有：

​       nf_conntrack内核模块；

​            当前追踪到的所有连接：/proc/net/nf_conntrack文件中；

​            能追踪的最大连接数量定义在：/proc/sys/net/nf_conntrack_max

​                此值可自行定义，建议必要时调整到足够大；

​            不同的协议的连接追踪的时长：/proc/sys/net/netfilter/

​            日志：/etc/syslog.conf，将数据包相关信息纪录在 /var/log 中，进行完此处理动作后，将会继续比对其它规则。



### RETURN

返回调用链



### LOG 

记录日志信息 

​          --log-prefix "STRING" 

​            iptables -I INPUT 6 -d 10.76.33.201 -p icmp --icmp-type 8 -j LOG --log-prefix "-- firewall log for icmp --" 

​           



### DNAT

目标地址转换 



### SNAT

源地址转换 



### REDIRECT

端口重定向 

​     

### MASQUERADE

地址伪装, 即SNAT

​     



### MARK

做防火墙标记



### 自定义链

由自定义链上的规则进行匹配检查





# 保存及重载规则



## iptables-save 保存现有规则

```
iptables-save > /PATH/TO/SOMEFILE
```

```
iptables-save > /etc/iptables.rules
```



## iptables-restore  导入规则

```
iptables-restore < /PATH/TO/SOMEFILE
```



```
iptables-restore < /etc/iptables.rules
```













>