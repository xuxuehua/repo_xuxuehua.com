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



### 翻页

使用**CTRL-B**和**CTRL-F**来进行翻页，它们的功能等同于PageUp和PageDown。

**CTRL-B**和**CTRL-F**前也可以加上数字，来表示向上或向下翻多少页。



ctrl+u 向上移动半页

ctrl+d 向下移动半页



### 文件中操作

命令"**gg**"移动到文件的第一行

而命令"**G**"则移动到文件的最后一行。

命令"**G**"前可以加上数字，在这里，数字的含义并不是倍数，而是你打算跳转的行号。例如，你想跳转到文件的第1234行，只需输入"**1234G**"。

你还可以按百分比来跳转，例如，你想跳到文件的正中间，输入"**50%**"；如果想跳到75%处，输入"**75%**"。注意，你必须先输入一个数字，然后输入"**%**"。如果直接输入"**%**"，那含义就完全不同了。"**:help N%**"阅读更多细节。



在文件中移动，你可能会迷失自己的位置，这时使用"**CTRL-G**"命令，查看一下自己位置。这个命令会显示出光标的位置及其它信息。为了避免迷失，你可以打开行号显示；使用"**:set number**"命令后，会在每一行前显示出行号，可以更方便的定位的跳转（**:help 'number'**）。



c 	删除并进入插入模式



### 移动到指定字符

上面的命令都是行间移动(除h, l外)，也就是从当前行移动到另外一行。如果我们想在当前行内快速移动，可以使用f, t, F, T命令。

"**f**"命令移动到光标右边的指定字符上，例如，"**fx**"，会把移动到光标右边的第一个'x'字符上。"**F**"命令则反方向查找，也就是移动到光标左边的指定字符上。

"**t**"命令和"**f**"命令的区别在于，它移动到光标右边的指定字符之前。例如，"**tx**"会移动到光标右边第一个'x'字符的前面。"**T**"命令是"**t**"命令的反向版本，它移动到光标左边的指定字符之后。

这四个命令只在当前行中移动光标，光标不会跨越回车换行符。

可以在命令前面使用数字，表示倍数。例如，"**3fx**"表示移动到光标右边的第3个'x'字符上。

"**;**"命令重复前一次输入的f, t, F, T命令，而"**,**"命令会反方向重复前一次输入的f, t, F, T命令。这两个命令前也可以使用数字来表示倍数。



### 行首/行尾

在vim中，移动到行首的命令非常简单，就是"**0**"，这个是数字0，而不是大写字母O。移动到行尾的命令是"**$**"。

另外还有一个命令"**^**"，用它可以移动到行首的第一个非空白字符。

在正则表达式中我们会看到，"**^**"字符代表行首，而"**$**"字符代表行尾。可见，vi/vim的按键的安排，的确是别具匠心的。



### 按单词移动

我们知道，英文文档的主体是单词，通常用空白字符(包括空格、制表符和回车换行符)来分隔单词。vim中提供了许多命令来按单词移动。

要根据单词来移动，首先要把文本分隔为一个个独立的单词。vim在对单词进行分隔时，会把'*iskeyword*'选项中的字符做为单词的组成字符。也就是说，一个单词(word)由'*iskeyword*'选项中定义的字符构成，它前面、后面的字符不在'*iskeyword*'选项定义的字符中。例如，如果我们把'*iskeyword*'选项设置为"*a-z,A-Z,48-57,_*"，那么"*FooBar_123*"被做为一个单词，而"*FooBar-123*"被做为三个单词："*FooBar*", "*-*"和"*123*"。"*a-z,A-Z,48-57,_*"中的48-57表示ASCII码表中的数字0-9。

vim中，移动光标到下一个单词的词首，使用命令"**w**"，移动光标到上一个单词的词首，使用命令"**b**"；移动光标到下一个单词的结尾，用命令"**e**"，移动光标到上一个单词的结尾，使用命令"**ge**"。

上面这些命令都使用'*iskeyword*'选项中的字符来确定单词的分界，还有几个命令，只把空白字符当做"*单词*"的分界。当然，这里说的"*单词*"已经不是传统意义上的单词了，而是由非空白字符构成一串字串。命令"**W**"移动光标到下个字串的开始，命令"**B**"移动到上个字串的开始；命令"**E**"移动到下个字串的结尾，命令"**gE**"移动到上个字串的结尾。和上面的命令比较一下，发现什么规律没有？





### H/M/L

**注意：**这几个命令是大写的。

使用H/M/L这三个键，可以让光标跳到当前窗口的顶部、中间、和底部，停留在第一个非空字符上。H命令和L命令前也可以加一个数字，但数字的含义不再是倍数，而是指距窗口顶部、底部的行数。例如，"**3H**"表示光标移动到距窗口顶部第3行的位置；"**5L**"表示光标移动到距窗口底部5行的位置。



### 相对于光标滚屏

在阅读代码时，有时我们需要根据光标所在的位置滚屏，把光标所在行移动窗口的顶端、中间或底部，这时就可以用到"**zt**"、"**zz**"和"**zb**"。这种滚屏方式相对于翻页来讲，它的好处在于，你能够始终以当前光标位置做为参照，不会出现翻几次页后，发现自己迷失了方向。



### 查找

查找，也可以做为一种快速移动的方式。

在vim中查找非常容易，直接在Normal模式下输入"**/**"，然后输入你想查询的字符串，回车，就跳转到第一个匹配的地方了。"**/**"是向下查找，而"**?**"进行反方向查找。命令"**n**"重复上一次的查找命令，而命令"**N**"也重复上一次的查找命令，只不过它按相反方向查找。

vim保存了查找的历史记录，你可以在输入"**/**"或"**?**"后，用上、下光标键(或CTRL-P/CTRL-N)翻看历史记录，然后再次执行这个查找。

另外你还可以使用"**q/**"和"**q?**"命令，在vim窗口最下面打开一个新的窗口，这个窗口会列出你的查找历史记录，你可以使用任何vim编辑命令对此窗口的内容进行编辑，然后再按回车，就会对光标所在的行的内容进行查找。



使用"**q/**"命令打开了command-line窗口，这个窗口列出了我之前所查找的字符串。我现在想查找包含"*check_swap*"，于是先跳到第399行，把"*check_tty*"改为"*check_swap*"，然后按回车。此时vim就去查找包含"*check_swap*"位置了。这个例子比较简单，你可能觉得command-line窗口没什么必要，但如果你要查找的内容是一个很长的正则表达式，你就会发现它非常有用了。

vim中有许多与查找相关的选项设置，其中最常用的是'*incsearch*', '*hlsearch*', '*ignorecase*'。

- '*incsearch*'表示在你输入查找内容的同时，vim就开始对你输入的内容进行匹配，并显示匹配的位置。打开这个选项，你可以即时看到查找的结果。
- '*hlsearch*'选项表示对匹配的所有项目进行高亮显示。
- '*ignorecase*'选项表示在查找时忽略大小写。

通常我会打开'*incsearch*'和'*hlsearch*'选项，关闭'*ignorecase*'选项。







### 利用跳转表 

在vim中，很多命令可以引起跳转，vim会记住把跳转前光标的位置记录到跳转表中，并提供了一些命令来根据跳转表进行跳转。要知道哪些命令引起跳转，参见"**:help jump-motions**"。

使用命令"**''**"（两个单引号）和"**``**"(两个反引号，在键盘上和"~"共用一个键)可以返回到最后跳转的位置。例如，当前光标位于文件中第1234行，然后我使用"**4321G**"命令跳转到第4321行；这时如果我按"**''**"或"**``**"，就会跳回到1234行。

因为这两个命令也属于跳转命令，所以第4321行也被记入跳转表，如果你再次使用这两个命令，就会发现自己又跳回第4321行了。

这两个命令有一点不同，"**``**"在跳转时会精确到列，而"**''**"不会回到跳转时光标所在的那一列，而是把光标放在第一个非空白字符上。

如果想回到更老的跳转位置，使用命令"**CTRL-O**"；与它相对应的，是"**CTRL-I**"，它跳转到更新的跳转位置(**:help CTRL-O**和**:help CTRL-I**)。这两个命令前面可以加数字来表示倍数。

使用命令"**:jumps**"可以查看跳转表(**:help :jumps**)。



### 使用标记

标记(mark)是vim提供的精确定位技术，其功能相当于GPS技术，只要你知道标记的名字，就可以使用命令直接跳转到该标记所在的位置。

vim中的标记都有一个名字，这个名字用单一的字符表示。大写和小写字母(A-Za-z)都可以做为标记的名字，这些标志的位置可以由用户来设置；而数字标记0-9，以及一些标点符号标记，用户不能进行设置，由vim来自动设置。

我们主要讲述字母标记的使用，对于数字标记和标点符号标记，请自行参阅帮助手册(**:help mark-motions**)。

小写字母标记局限于缓冲区，也就是说，每个缓冲区都可以定义自己的小写字母标记，各缓冲区间的小写字母标记彼此不干扰。如果我在文件A中设置一个标记t，然后在文件B中也可以设置一个标记t。那么在文件A中，可以用"**'t**"命令跳到文件A的标记t位置 ；在文件B中，可以用"**'t**"命令跳到文件B的标记t位置。如果文件在缓冲区列表中被删除，小写字母标记就丢失了。

大写字母标记是全局的，它在文件间都有效。如果在文件A中定义一个标记T，那么当使用命令"**'T**"时，就会跳到文件A的标记T位置，不管你当前处于哪个文件中。

设定一个标记很简单，使用命令"**m{a-zA-Z}**"就可以了。例如，命令"**mt**"在把当前光标位置设定为标记t；命令"**mT**"把当前光标位置设定为标记T。(**:help m**)

要跳转到指定的标记，使用命令"**'{a-zA-Z}**"或"**{a-zA-Z}**"。例如，命令"**'t**"会跳转到标记t；命令"**'T**"会跳转到标记T。( **:help '**)

单引号和反引号的区别和上面所讲的一样，"**`**"在跳转时会精确到列，而"**'**"不会回到跳转时光标所在的那一列，而是把光标放在第一个非空白字符上。

标记也可以被删除，使用命令"**:delmarks**"可以删除指定标记。命令"**:marks**"列出所有的标记。

关于标记，有两个非常有用的插件，一个是ShowMarks，另外一个叫marks browser。

[ShowMarks](http://www.vim.org/scripts/script.php?script_id=152)是我最常用的插件之一，它使用vim提供的sign功能以及高亮功能显示出标记的位置。这样，你在设定了一个标记后，它就会在你的vim窗口中显示出标记的名字，并高亮这一行。

在你的$HOME/.vim目录把它解压，然后进行简单设置。 在我的vimrc中，对ShowMarks进行了如下配置：

```
""""""""""""""""""""""""""""""
" showmarks setting
""""""""""""""""""""""""""""""
" Enable ShowMarks
let showmarks_enable = 1
" Show which marks
let showmarks_include = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
" Ignore help, quickfix, non-modifiable buffers
let showmarks_ignore_type = "hqm"
" Hilight lower & upper marks
let showmarks_hlline_lower = 1
let showmarks_hlline_upper = 1 
```

首先，使能showmarks插件，然后定义showmarks只显示全部的大写标记和小写，并高亮这两种标记；对文件类型为help、quickfix和不可修改的缓冲区，则不显示标记的位置。

你可以定义自己的颜色来高亮标记所在的行，下面是我的定义，我把它放在[我自己的colorscheme文件](https://blog.easwy.com/archives/advanced-vim-skills-syntax-on-colorscheme/)中：

```
" For showmarks plugin
hi ShowMarksHLl ctermbg=Yellow   ctermfg=Black  guibg=#FFDB72    guifg=Black
hi ShowMarksHLu ctermbg=Magenta  ctermfg=Black  guibg=#FFB3FF    guifg=Black 
```

ShowMarks插件中已经定义了一些快捷键：

```
<Leader>mt   - 打开/关闭ShowMarks插件
<Leader>mo   - 强制打开ShowMarks插件
<Leader>mh   - 清除当前行的标记
<Leader>ma   - 清除当前缓冲区中所有的标记
<Leader>mm   - 在当前行打一个标记，使用下一个可用的标记名 
```

我最常使用的是"**<Leader>mm**"和"**<Leader>mh**"，用起来非常方便。在我的vimrc中，把[Leader定义为"*,*"](https://blog.easwy.com/archives/advanced-vim-skills-introduce-vimrc/)，所以每次都使用"**,mm**"和"**,mh**"来设置和删除mark。

在vim 7.0中，如果大写的标记被定义了，那么函数line()无论在哪个缓冲区里都会返回该标记的行号，导致showmarks在每个缓冲区里都会把这个大写标记显示出来。因此我为这个插件打了个补丁来修正此问题。

vim 7.0中也可以真正的删除一个mark标记，所以也改了showmarks插件的删除标记功能。原来的功能在删除标记时，并未真正删除它，只是把这个标记指向缓冲区的第一行；现在则是真正删除此标记。





### 折行

在文件比较大时，在文件中移动也许会比较费力。这个时候，你可以根据自己的需要把暂时不会访问的文本折叠起来，既减少了对空间的占用，移动速度也会快很多。

vim提供了多种方法来进行折叠，既可以手动折叠，也可以根据缩进、语法，或使用表达式来进行折叠。

程序文件一般都具有良好的结构，所以根据语法进行折叠是一个不错的选择。

要启用折叠，首先要使能'*foldenable*'选项，这个选项是局部于窗口的选项，因此可以为每个窗口定义不同的折叠。

接下来，设置'*foldmethod*'选项，对于程序，我们可以选择根据语法高亮进行折叠。需注意的，要根据语法高亮进行折叠，必须打开文件类型检测和语法高亮功能，请参见我前面的文章。

下面是我的vimrc中的设置，它使用了自动命令，如果发现文件类型为c或cpp，就启用折叠功能，并按语法进行折叠：

```
autocmd FileType c,cpp  setl fdm=syntax | setl fen 
```

注意，vim的很多命令、选项名都有简写形式，在帮助手册中可以看到简写形式，也可以按简写形式来help，例如，要查看'*foldmethod*'选项的帮助，可以只输入"**:help 'fdm'**"。



### ZZ

没有修改文件直接退出，如果修改了文件保存后退出







## 插入模式

输入 i 或 a 进入插入模式







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





### set

To turn off autoindent when you paste code, there's a special "paste" mode.

Type

```
:set paste
```

Then paste your code. Note that the text in the tooltip now says `-- INSERT (paste) --`.

After you pasted your code, turn off the paste-mode, so that auto-indenting when you type works correctly again.

```
:set nopaste
```

However, I always found that cumbersome. That's why I map `<F3>` such that it can switch between paste and nopaste modes *while editing the text!* I add this to `.vimrc`

```
set pastetoggle=<F3>
```



# vimrc

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

