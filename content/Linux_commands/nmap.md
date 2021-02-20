---
title: "nmap"
date: 2019-09-29 23:35
---
[TOC]



# nmap 





## -p

```
# nmap -Pn -p T:9042 10.33.1.33

Starting Nmap 6.40 ( http://nmap.org ) at 2020-09-17 06:22 UTC
Nmap scan report for ip-10-33-1-33.ec2.internal (10.33.1.33)
Host is up (0.00082s latency).
PORT     STATE SERVICE
9042/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 0.10 seconds
```



filtered status

```
# nmap -Pn -p T:59718 1.1.1.1

Starting Nmap 7.60 ( https://nmap.org ) at 2021-02-13 04:59 PST
Nmap scan report for 120.209.53.67
Host is up (0.20s latency).

PORT      STATE    SERVICE
59718/tcp filtered unknown

Nmap done: 1 IP address (1 host up) scanned in 2.53 seconds
```



## -sP host is up or not

```
# nmap -sP 1111

Starting Nmap 7.60 ( https://nmap.org ) at 2021-02-13 05:21 PST
Nmap scan report for 111
Host is up (0.19s latency).
Nmap done: 1 IP address (1 host up) scanned in 2.61 seconds
```



### Network range

```
nmap -sP 172.16.0.0/12
```





## -sT  check service

```
# nmap -sT 1111

Starting Nmap 7.60 ( https://nmap.org ) at 2021-02-13 05:16 PST
Nmap scan report for 111
Host is up (0.20s latency).
Not shown: 988 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
25/tcp    filtered smtp
80/tcp    filtered http
139/tcp   filtered netbios-ssn
443/tcp   filtered https
445/tcp   filtered microsoft-ds
587/tcp   filtered submission
593/tcp   filtered http-rpc-epmap
2525/tcp  filtered ms-v-worlds
8080/tcp  filtered http-proxy
10009/tcp open     swdtp-sv
34571/tcp open     unknown

Nmap done: 1 IP address (1 host up) scanned in 9.03 seconds
```



## -O  os version

```
# nmap -O 1111

Starting Nmap 7.60 ( https://nmap.org ) at 2021-02-13 05:25 PST
Nmap scan report for 1111
Host is up (0.19s latency).
Not shown: 988 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
25/tcp    filtered smtp
80/tcp    filtered http
139/tcp   filtered netbios-ssn
443/tcp   filtered https
445/tcp   filtered microsoft-ds
587/tcp   filtered submission
593/tcp   filtered http-rpc-epmap
2525/tcp  filtered ms-v-worlds
8080/tcp  filtered http-proxy
10009/tcp open     swdtp-sv
34571/tcp open     unknown
Device type: general purpose
Running: Linux 3.X|4.X
OS CPE: cpe:/o:linux:linux_kernel:3 cpe:/o:linux:linux_kernel:4
OS details: Linux 3.2 - 4.8
Network Distance: 16 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 23.23 seconds
```



## -F

```
# nmap -F -sT -v 1111

Starting Nmap 7.60 ( https://nmap.org ) at 2021-02-13 05:23 PST
Initiating Ping Scan at 05:23
Scanning 1111 [4 ports]
Completed Ping Scan at 05:23, 0.43s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 05:23
Completed Parallel DNS resolution of 1 host. at 05:23, 1.85s elapsed
Initiating Connect Scan at 05:23
Scanning 1111 [100 ports]
Discovered open port 22/tcp on 1111
Completed Connect Scan at 05:23, 2.50s elapsed (100 total ports)
Nmap scan report for 1111
Host is up (0.19s latency).
Not shown: 92 closed ports
PORT     STATE    SERVICE
22/tcp   open     ssh
25/tcp   filtered smtp
80/tcp   filtered http
139/tcp  filtered netbios-ssn
443/tcp  filtered https
445/tcp  filtered microsoft-ds
587/tcp  filtered submission
8080/tcp filtered http-proxy

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 4.87 seconds
           Raw packets sent: 4 (152B) | Rcvd: 1 (28B)
```

