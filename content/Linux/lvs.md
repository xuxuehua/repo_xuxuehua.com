---
title: "lvs"
date: 2020-04-07 12:53
---
[toc]



# lvs 



LVS  Linux Virtual Server  不能和iptables共存

本身也是工作在内核上工作在input链上，强行修改规则，转发到postrouting发出去





ipvsadm（用户空间） 管理集群服务的命令行工具

ipvs （内核空间）





## LVS-NAT 地址转换

  集群节点跟director必须在同一个IP网络中

  RIP通常是私有IP地址，仅用于各个集群节点间的通信

  director位于client和real server之间，并负责处理进出的所有通信

  real server必须将网关指向DIP

  支持端口映射

  real server 可以是任意操作系统

  较大规模应用场景中，director易称为系统瓶颈  



## LVS-DR 直接路由 （一般在生产环境中使用）

  director并不修改任何IP地址，而是通过修改mac地址进行转发。

  director仅处理入站请求

  各个集群节点必须和director在同一个物理网络中

  RIP地址可以不用是私有地址，使用公网地址便于远程管理和监控

  director仅负责处理入站请求，响应报文则由director直接发往客户端

  real server 不能将网关指向DIP

  不支持端口映射



## LVS-TUN 隧道 （用的很少）

  集群节点可以跨越互联网Internet

  real server的RIP必须是公网地址

  director 仅负责处理入站请求，响应报文则由real server直接发往客户端

  real server 网关不能指向director

  只有支持隧道功能的OS才能用于real server

  不支持端口映射





# 调度方法

## 静态调度方法

  rr： Round Robin 轮询，轮调

  wrr：Weight Round Robin 加权的

  sh： Source Hash， 源地址hash。 根据源地址，绑定session (Session Affilinity)

  dh：Destination Hash，以目标地址为标准挑选



## 动态调度方法

  lc： 最少连接

​     active * 256 + inactive

​     比较后，谁的小就挑选谁

  

  wlc： 加权最少连接 （lvs 默认方式）

​     ( active * 256 + inactive ) / weight ,

​     取最小值

  sed： 最短期望延迟

​     不再计算非活动连接

​     ((active+1) * 256)  / weigth

​     取最小值

   

  nq:  Never Queue 

​     

  LBLC： Locality-Based Least-Connection 基于本地的最少连接

​     类似DH

​     

  LBLCR:  Replication 基于本地的，带复制功能的最少连接







# example



## health_check.sh

```
#!/usr/bin/env bash#declare -a RSSTATUS
VIP=192.168.10.3
CPORT=80FAIL_BACK=127.0.0.1
RS=("192.168.10.7" "192.168.10.8")
RW=("2" "1")
RPORT=80TYPE=g
CHKLOOP=3LOG=/var/log/ipvsmonitor.log

addrs() {
    ipvsadm -a -t $VIP:$CPORT -r $1:$RPORT -$TYPE -w $2
    [ $? -eq 0 ] && return 0 || return 1}

delrs() {
    ipvsadm -d -t $VIP:$CPORT -r $1:$RPORT
    [ $? -eq 0 ] && return 0 || return 1}

checkrs() {
    local I=1    while [ $I -le $CHKLOOP ]; do        if curl --connect-timeout 1 http://$1 &> /dev/null; then            return 0        fi        let I++
    done    return 1}

initstatus() {
    local I
    local COUNT=0;
    for I in ${RS[*]}; do        if checkrs $I; then            RSSTATUS[$COUNT]=1        else            RSSTATUS[$COUNT]=0        fi    let COUNT++
    done}

initstatus
while :; do    let COUNT=0    for I in ${RS[*]}; do        if checkrs $I; then            if [ ${RSSTATUS[$COUNT]} -eq 0 ]; then                addrs $I ${RW[$COUNT]}
                [ $? -eq 0 ] && RSSTATUS[$COUNT]=1 && echo "`date + '%F %H:%M:%S'`, $I is back." >> $LOG            fi        else            if [ ${RSSTATUS[$COUNT]} -eq 1 ]; then                delrs $I
                [ $? -eq 0 ] && RSSTATUS[$COUNT]=0 && echo "`date + '%F %H:%M:%S'`, $I is gone." >> $LOG            fi        fi        let COUNT++
    done    sleep 5done
```

