---
title: "vim"
date: 2018-12-24 00:07
---


[TOC]



# VIM



## 历史 

Vim 源于 Vi，但不是 Vi，Vi 作为计算机的文本编辑器历史极为悠远，它是由美国计算机科学家比尔·乔伊编写并于1976年发布的

Vim 诞生的要晚一些，它的第一个版本由布莱姆·米勒在1991年发布

随着 Vim 的不断发展，更多更好的功能被加了进来，正式名称改成了 Vi IMproved（增强），也就形成了现代的 Vim





## 正常模式

### 方向

```
h 或 向左方向鍵(←)      游標向左移動一個字元
j 或 向下方向鍵(↓)      游標向下移動一個字元
k 或 向上方向鍵(↑)      游標向上移動一個字元
l 或 向右方向鍵(→)      游標向右移動一個字元
```



### 页面

```
[Ctrl] + [f]      螢幕『向下』移動一頁，相當於 [Page Down]按鍵 (常用)

[Ctrl] + [b]      螢幕『向上』移動一頁，相當於 [Page Up] 按鍵 (常用)
```



G      移動到這個檔案的最後一行(常用)



c 	删除并进入插入模式



### 查找

```
/word      向游標之下尋找一個名稱為 word 的字串。例如要在檔案內搜尋 vbird 這個字串，就輸入 /vbird 即可！ (常用)

?word      向游標之上尋找一個字串名稱為 word 的字串。

f 				向后查找
F					向前查找
```





## 插入模式

输入 i 或 a 进入插入模式





## 可视模式



## 底行/命令模式

通过「: / ? !」可以进入命令模式，分别对应的是：执行内部命令、向上或向下搜索、执行外部命令





### help 帮助

```
:help
```



### colo 颜色

/usr/share/vim/vim72/colors

```
blue.vim
darkblue.vim
default.vim
delek.vim
desert.vim
elflord.vim
evening.vim
koehler.vim
morning.vim
murphy.vim
pablo.vim
peachpuff.vim
ron.vim
shine.vim
slate.vim
torte.vim
zellner.vim
```



### 读文件

不知道经常用vim的同学有没有一个体验，经常会打开一个文件、复制内容、关闭文件、打开另一个文件、然后粘贴进去复制到内容。编辑器之神难道体验这么差？其实有更好的办法，那就是：

```javascript
:read filename
```



### 缓冲区跳转

`ctrl + ^` 是最常用的方式，来切换当前缓冲区和上一个缓冲区。这样非常方便来回编辑两个文件。缓冲区还提供了很多跳转命令：

```javascript
:ls, :buffers      列出所有缓冲区
:bn[ext]            下一个缓冲区
:bp[revious]        上一个缓冲区
:b {number, expression}    跳转到指定缓冲区
```

`:ls` 然后输入编号是我常用的一种方式，可以快速跳转到对应文件。





### 展示窗口

当修改某一个窗口的文件时，其他窗口都会同步更新

```
:split filename 或 :vsplit filename 命令在多个窗口打开一个文件
```



```
:tabedit filename 在另一个标签页中打开一个文件，在这个标签页中又可以打开多个窗口。
```

使用 ctrl + w 可以在个窗口之间跳转，使用 ctrl + 方向键可以按照方向切换窗口。



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





### 统计文本行数和当前光标的位置

```
ctrl + g
```



### 统计字节数

```
g + ctrl + g
```



### 文本行翻转

```
:g/^/m 0
```







### 插入一个1到100的序列

```
:r!seq 100
```



### 每一行文字前面增加序号

```
:let i=1 | g /^/ s//\=i.". "/ | let i+=1
```



### 所有后缀为 java 的文件中的 apache 替换成 eclipse

当前目录执行

```
vim
:n **/*.java
:argdo %s/apache/eclipse/ge | update 
```





## vimrc

用户目录下的.vimrc文件就是Vim针对当前用户的主配置文件，该文件不是必备的，没有的话就创建它。文件位于当前用户的主目录下，可以用 ~/.vimrc 找到，Vim启动时会自动运行文件中的每条命令。

`.vimrc`的注释用双引号"表示

```
syn on                      "语法支持

# 然后在  set backspace=2下面一行插入如下代码
set ai                  " auto indenting
set ruler               " show the cursor position
set hlsearch            " highlight the last searched term
set history=1000        " keep 1000 lines of history
syntax on               " syntax highlighting
filetype plugin on      " use the file type plugins

set bs=2                    "在insert模式下用退格键删除
set showmatch               "代码匹配
set laststatus=2            "总是显示状态行
set expandtab               "以下三个配置配合使用，设置tab和缩进空格数
set shiftwidth=4
set tabstop=4
set cursorline              "为光标所在行加下划线
set number                  "显示行号
set autoread                "文件在Vim之外修改过，自动重新读入

set ignorecase              "检索时忽略大小写
set fileencodings=uft-8,gbk "使用utf-8或gbk打开文件
set hls                     "检索时高亮显示匹配项
set helplang=cn             "帮助系统设置为中文
set foldmethod=syntax       "代码折叠

# 其它选项
set nocompatible                 "去掉有关vi一致性模式，避免以前版本的bug和局限    
set nu!                          "显示行号
set guifont=Luxi/ Mono/ 9        "设置字体，字体名称和字号
filetype on                      "检测文件的类型     
set history=1000                 "记录历史的行数
set background=dark              "背景使用黑色
syntax on                        "语法高亮度显示
set autoindent                   "vim使用自动对齐，也就是把当前行的对齐格式应用到下一行(自动缩进）
set cindent				         "（cindent是特别针对 C语言语法自动缩进）
set smartindent                  "依据上面的对齐格式，智能的选择对齐方式，对于类似C语言编写上有用   
set tabstop=4                    "设置tab键为4个空格，
set shiftwidth =4                "设置当行之间交错时使用4个空格     
set ai!                          "设置自动缩进 
set showmatch                    "设置匹配模式，类似当输入一个左括号时会匹配相应的右括号      
set guioptions-=T                "去除vim的GUI版本中得toolbar   
set vb t_vb=                     "当vim进行编辑时，如果命令错误，会发出警报，该设置去掉警报       
set ruler                        "在编辑过程中，在右下角显示光标位置的状态行     
set nohls                        "默认情况下，寻找匹配是高亮度显示，该设置关闭高亮显示     
set backspace=2                  "设置退格键可用
set incsearch                    "在程序中查询一单词，自动匹配单词的位置；如查询desk单词，当输到/d时，	

" conf for tabs, 为标签页进行的配置，通过ctrl h/l切换标签等
let mapleader = ','
nnoremap <C-l> gt
nnoremap <C-h> gT
nnoremap <leader>t : tabe<CR>

"conf for plugins {{ 插件相关的配置
"状态栏的配置 
"powerline{
set guifont=PowerlineSymbols\ for\ Powerline
set nocompatible
set t_Co=256
let g:Powerline_symbols = 'fancy'
"}
"pathogen是Vim用来管理插件的插件
"pathogen{
call pathogen#infect()
"}

"}}
```

