---
title: "regular_expression"
date: 2019-06-26 19:15
---
[TOC]

# 元字符



# Regular Expression

文本字符和正则表达式的元字符组合而成的匹配条件



```
   .     匹配任意单个字符
     *     匹配其前面的字符任意次     （这个是文件通配的方式不一样）
     .*     匹配任意长度的任意字符
     \?     匹配其前面的字符一次或0次
     \{m,n} 匹配其前面的字符，至少M次，至多N次
          \{1,} 至少一次，没有上限
          \{0,3} 至少0次，至多3次
     ^     锚定行首，此字符后面的任意内容必须出现在行首
     $     锚定行尾，此字符前面的任意内容必须出现在行尾
     ^$     空白行
     \<     or \b     其后面的任意字符必须作为单词首部出现
     \>     or \b     其前面的任意字符必须作为单词尾部出现
          \<root>\      必须以root单个单词出现     same as  \broot\b
     \(\)     把内容分组
           \(ab\)*     即ab可以出现0次1次或任意次，可以出现所有字符
           后项引用
           \1:     引用第一个左括号以及与之对应的右括号所包括的所有内容
                    exp;     grep ‘\(l..e).*\1’ test3.txt               即前面匹配了l..e     那后面的\1也会再次引用
                    exp      grep ‘\(0-9\).*\1$’ /etc/inittab        中间含有任意数字，并以相同数字结尾的匹配
           \2
           \3
           \n

     下面和globbing一样
     [ ]      匹配指定范围内的任意单个字符
     [^ ]     匹配指定范围外的任意单个字符
     字符集合 [:digit:]      [:space:]
```



