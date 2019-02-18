---
title: "vim"
date: 2018-09-05 00:46
---

[TOC]

# VIM 



## 底行模式

### 读文件

不知道经常用vim的同学有没有一个体验，经常会打开一个文件、复制内容、关闭文件、打开另一个文件、然后粘贴进去复制到内容。编辑器之神难道体验这么差？其实有更好的办法，那就是：

```javascript
:read filename
```



### 缓冲区跳转

刚用`vim`的很长一段时间都对多文件编辑特别不习惯，知道后面明白自己忽略了缓冲区的作用。`ctrl + ^` 是最常用的方式，来切换当前缓冲区和上一个缓冲区。这样非常方便来回编辑两个文件。缓冲区还提供了很多跳转命令：

```javascript
:ls, :buffers      列出所有缓冲区
:bn[ext]            下一个缓冲区
:bp[revious]        上一个缓冲区
:b {number, expression}    跳转到指定缓冲区
```

`:ls` 然后输入编号是我常用的一种方式，可以快速跳转到对应文件。



### 搜索

用`vimgrep`还是比较快捷的。

```javascript
vimgrep /匹配模式/[g][j] 要搜索的文件/范围
g：表示是否把每一行的多个匹配结果都加入
j：表示是否搜索完后定位到第一个匹配位置

vimgrep /pattern/ %           在当前打开文件中查找
vimgrep /pattern/ *             在当前目录下查找所有
vimgrep /pattern/ **            在当前目录及子目录下查找所有
vimgrep /pattern/ *.c          查找当前目录下所有.c文件
vimgrep /pattern/ **/*         只查找子目录

cn                             查找下一个
cp                             查找上一个
cw                            打开quickfix
```

在`quickfix`里面一样可以快捷的跳转。



### 区域选择

区域选择也是个非常常用的命令，其命令格式为

```javascript
<action>a<object> 和 <action>i<object>
```

- action可以是任何的命令，如 d (删除), y (拷贝), v (可以视模式选择)。
- object 可能是： w 一个单词， W 一个以空格为分隔的单词， s 一个句字， p 一个段落。也可以是一个特别的字符："、 '、 )、 }、 ]。



### 宏录制

```javascript
qa 把你的操作记录在寄存器 a。
@a 会replay被录制的宏。
@@ 是一个快捷键用来replay最新录制的宏。
```