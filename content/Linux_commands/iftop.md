---
title: "iftop"
date: 2018-11-06 23:45
---


[TOC]


# iftop

实时流量监控

display bandwidth usage on an interface by host



## SYNOPSIS

`iftop -h | [-nNpblBP][-i interface] [-f filter code][-F net/mask] [-G net6/mask6]`



-h     Print a summary of usage.

-n     Don't do hostname lookups.

-N     Do not resolve port number to service names

-p     Run in promiscuous mode, so that traffic which does not pass directly through the specified interface is also counted.

-P     Turn on port display.

-l     Display and count datagrams addressed to or from link-local IPv6 addresses.  The default is not to display that address category.

-b     Don't display bar graphs of traffic.

-m limit
​       Set the upper limit for the bandwidth scale.  Specified as a number with a 'K', 'M' or 'G' suffix.

-B     Display bandwidth rates in bytes/sec rather than bits/sec.

-i interface
​      Listen to packets on interface.


-f filter code
​              Use filter code to select the packets to count. Only IP packets are ever counted, so the specified code is evaluated as (filter code) and ip.

-F net/mask
​       Specifies  an IPv4 network for traffic analysis.  If specified, iftop will only include packets flowing in to or out of the given network, and packet direction
​       is determined relative to the network boundary, rather than to the interface.  You may specify mask as a dotted quad, such as /255.255.255.0, or  as  a  single
​       number specifying the number of bits set in the netmask, such as /24.

-G net6/mask6
​       Specifies  an IPv6 network for traffic analysis. The value of mask6 can be given as a prefix length or as a numerical address string for more d bitmask‐
​       ing.

-c config file
​       Specifies an alternate config file.  If not specified, iftop will use ~/.iftoprc if it exists.  See below for a description of config files

-t text output mode
​       Use text interface without ncurses and print the output to STDOUT.



