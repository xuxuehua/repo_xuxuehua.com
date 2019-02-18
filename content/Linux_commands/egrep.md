---
title: "egrep"
date: 2018-10-03 19:17
---


[TOC]


# egrep

等同于 grep -E

?     意义相同，但是不需要\

\+     匹配其前字符至少一次     相当于基本exgexp中的\(1,\)

{m,n}     意义与基本exgexp相同，不需要\

( )     分组，不需要\

|     表示or           exp     (C|c)at    表示Cat or cat



## -o 打印匹配项

-o --only-matching  Print only the matched(not-empty) parts of a matching line.



## -v 打印版本信息

-v --version   Print the version number of grep to the standard output stream.



抓去1-255 之间的整数，小数点是不会被识别的

egrep  --color ‘\<([1-9] | [1-9][0-9] | 1[0-9][0-9] | 2[0-4][0-9] | 25[0-5])\>'