---
title: "argparse"
date: 2018-09-09 10:41
---


[TOC]


# argparse

python的命令行解析工具，或者说可以在python代码中调用shell的一些命令，从而简化和系统命令之间的交互。





## 解析器对象 ArgumentParser

创建解析器对象ArgumentParser，可以添加参数。

```
class ArgumentParser(prog=None, usage=None, description=None, epilog=None, parents=[], formatter_class=argparse.HelpFormatter, prefix_chars='-', fromfile_prefix_chars=None, argument_default=None, conflict_handler='error', add_help=True)
```



### prog 程序名称

程序的名字，默认为sys.argv[0]，用来在help信息中描述程序的名称。

```
parser = argparse.ArgumentParser(prog='myprogram')
parser.print_help()
```



### usage 

描述程序用途的字符串

```
parser = argparse.ArgumentParser(prog='PROG', usage='%(prog)s [options]')
```



### description 帮助描述

help信息前的文字

```
parser=argparse.ArgumentParser(description="This is a example program ")
```



### epilog

help信息之后的信息

```
parser = argparse.ArgumentParser(description='A foo that bars', epilog="And that's how you'd foo a bar")
```



### parents

由ArgumentParser对象组成的列表，它们的arguments选项会被包含到新ArgumentParser对象中

```
 parent_parser = argparse.ArgumentParser(add_help=False)
 parent_parser.add_argument('--parent', type=int)
 foo_parser = argparse.ArgumentParser(parents=[parent_parser])
 foo_parser.add_argument('foo')
 foo_parser.parse_args(['--parent', '2', 'XXX'])
 
 Namespace(foo='XXX', parent=2)
```



### formatter_class

help信息输出的格式





### prefix_chars

参数前缀，默认为'-'

```
>>> parser = argparse.ArgumentParser(prog='PROG', prefix_chars='-+')
>>> parser.add_argument('+f')
>>> parser.add_argument('++bar')
>>> parser.parse_args('+f X ++bar Y'.split())
Namespace(bar='Y', f='X')
```



### fromfile_prefix_chars 

前缀字符，放在文件名之前

```
>>> with open('args.txt', 'w') as fp:
...    fp.write('-f\nbar')
>>> parser = argparse.ArgumentParser(fromfile_prefix_chars='@')
>>> parser.add_argument('-f')
>>> parser.parse_args(['-f', 'foo', '@args.txt'])
Namespace(f='bar')
```



### argument_default 

参数的全局默认值。例如，要禁止parse_args时的参数默认添加

```
>>> parser = argparse.ArgumentParser(argument_default=argparse.SUPPRESS)
>>> parser.add_argument('--foo')
>>> parser.add_argument('bar', nargs='?')
>>> parser.parse_args(['--foo', '1', 'BAR'])
Namespace(bar='BAR', foo='1')
>>> parser.parse_args()
Namespace()
```



### conflict_handler

解决冲突的策略，默认情况下冲突会发生错误

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-f', '--foo', help='old foo help')
>>> parser.add_argument('--foo', help='new foo help')
Traceback (most recent call last):
 ..
ArgumentError: argument --foo: conflicting option string(s): --foo


```

```
parser = argparse.ArgumentParser(prog='PROG', conflict_handler='resolve')
>>> parser.add_argument('-f', '--foo', help='old foo help')
>>> parser.add_argument('--foo', help='new foo help')
>>> parser.print_help()
usage: PROG [-h] [-f FOO] [--foo FOO]
 
optional arguments:
 -h, --help  show this help message and exit
 -f FOO      old foo help
 --foo FOO   new foo help
```



### add_help 显示help提示

设为False时，help信息里面不再显示-h --help信息

默认为True





## 命令参数 add_argument()

add_argument()方法，用来指定程序需要接受的命令参数

```
add_argument(name or flags...[, action][, nargs][, const][, default][, type][, choices][, required][, help][, metavar][, dest])
```



### name or flags





#### 位置参数

解析时没有位置参数就会报错

```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("echo", help="echo the string you use here")
args = parser.parse_args()
print args.echo

>>>
$ python prog.py -h
usage: prog.py [-h] echo

positional arguments:
  echo        echo the string you use here

optional arguments:
  -h, --help  show this help message and exit
```

> 方法parse_args()通过分析指定的参数返回一些数据，如本例中的echo
>
> 因为如果不指定参数类型，argparse默认它是字符串







#### 可选参数

```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--verbosity", help="increase output verbosity")
args = parser.parse_args()
if args.verbosity:
    print "verbosity turned on"
    
>>>
$ python prog.py --verbosity 1
verbosity turned on
$ python prog.py
$ python prog.py --help
usage: prog.py [-h] [--verbosity VERBOSITY]

optional arguments:
  -h, --help            show this help message and exit
  --verbosity VERBOSITY
                        increase output verbosity
$ python prog.py --verbosity
usage: prog.py [-h] [--verbosity VERBOSITY]
prog.py: error: argument --verbosity: expected one argument
```

> 当指定--verbosity时程序就显示一些东西，没指定的时候就不显示。



#### 参数简写

```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("-v", "--verbose", help="increase output verbosity",
                    action="store_true")
args = parser.parse_args()
if args.verbose:
    print "verbosity turned on"
    
>>>
$ python prog.py -v
verbosity turned on
$ python prog.py --help
usage: prog.py [-h] [-v]

optional arguments:
  -h, --help     show this help message and exit
  -v, --verbose  increase output verbosity
```







### action

默认为store

store_const，值存放在const中：

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='store_const', const=42)
>>> parser.parse_args('--foo'.split())
Namespace(foo=42)
```



store_true和store_false，值存为True或False

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='store_true')
>>> parser.add_argument('--bar', action='store_false')
>>> parser.add_argument('--baz', action='store_false')
>>> parser.parse_args('--foo --bar'.split())
Namespace(bar=False, baz=True, foo=True)
```



append：存为列表

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', action='append')
>>> parser.parse_args('--foo 1 --foo 2'.split())
Namespace(foo=['1', '2'])
```



append_const：存为列表，会根据const关键参数进行添加：

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--str', dest='types', action='append_const', const=str)
>>> parser.add_argument('--int', dest='types', action='append_const', const=int)
>>> parser.parse_args('--str --int'.split())
Namespace(types=[<type 'str'>, <type 'int'>])
```



count：统计参数出现的次数

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--verbose', '-v', action='count')
>>> parser.parse_args('-vvv'.split())
Namespace(verbose=3)
```



help：help信息

version：版本

```
>>> import argparse
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('--version', action='version', version='%(prog)s 2.0')
>>> parser.parse_args(['--version'])
PROG 2.0
```



### nargs 参数数量

值可以为整数N(N个)，*(任意多个)，+(一个或更多)， ? (可有可无)

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs='*')
>>> parser.add_argument('--bar', nargs='*')
>>> parser.add_argument('baz', nargs='*')
>>> parser.parse_args('a b --foo x y --bar 1 2'.split())
Namespace(bar=['1', '2'], baz=['a', 'b'], foo=['x', 'y'])
```



值为？时，首先从命令行获得参数，若没有则从const获得，然后从default获得：

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', nargs='?', const='c', default='d')
>>> parser.add_argument('bar', nargs='?', default='d')
>>> parser.parse_args('XX --foo YY'.split())
Namespace(bar='XX', foo='YY')
>>> parser.parse_args('XX --foo'.split())
Namespace(bar='XX', foo='c')
>>> parser.parse_args(''.split())
Namespace(bar='d', foo='d')
```



更常用的情况是允许参数为文件

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('infile', nargs='?', type=argparse.FileType('r'),
...                     default=sys.stdin)
>>> parser.add_argument('outfile', nargs='?', type=argparse.FileType('w'),
...                     default=sys.stdout)
>>> parser.parse_args(['input.txt', 'output.txt'])
Namespace(infile=<open file 'input.txt', mode 'r' at 0x...>,
          outfile=<open file 'output.txt', mode 'w' at 0x...>)
>>> parser.parse_args([])
Namespace(infile=<open file '<stdin>', mode 'r' at 0x...>,
          outfile=<open file '<stdout>', mode 'w' at 0x...>)
```



### const
保存一个常量




### default 不提供该参数
default设置它的默认值为0，这样可以让它兼容其他整型。注意，如果一个可选参数没有指定，它就会被设置成None，None是不能和整型比较的(触发TypeError异常)

一般和？* 配合使用

```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int,
                    help="display a square of a given number")
parser.add_argument("-v", "--verbosity", action="count", default=0,
                    help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbosity >= 2:
    print "the square of {} equals {}".format(args.square, answer)
elif args.verbosity >= 1:
    print "{}^2 == {}".format(args.square, answer)
else:
    print answer

>>>
$ python prog.py 4
16
```



### type
参数类型, 指定argparse 类型

```
parser = argparse.ArgumentParser()
parser.add_argument("square", help="display a square of a given number",
                    type=int)
```



### choices
可供选择的值

```
>>> parser = argparse.ArgumentParser(prog=``'doors.py'``)
>>> parser.add_argument(``'door'``, type=``int``, choices=range(1, 4))
>>> print(parser.parse_args([``'3'``]))
Namespace(door=3)
>>> parser.parse_args([``'4'``])
usage: doors.py [-h] {1,2,3}
doors.py: error: argument door: invalid choice: 4 (choose ``from` `1, 2, 3)
```



### required

是否必选





### desk

可作为参数名

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo', dest='bar')
>>> parser.parse_args('--foo XXX'.split())
Namespace(bar='XXX')
```



## 解析参数

最常见的空格分开

```
>>> parser = argparse.ArgumentParser(prog='PROG')
>>> parser.add_argument('-x')
>>> parser.add_argument('--foo')
>>> parser.parse_args('-x X'.split())
Namespace(foo=None, x='X')
>>> parser.parse_args('--foo FOO'.split())
Namespace(foo='FOO', x=None)
```



长选项用=分开

```
>>> parser.parse_args('--foo=FOO'.split())
Namespace(foo='FOO', x=None)
```



短选项可以写在一起：

```
>>> parser.parse_args('-xX'.split())
Namespace(foo=None, x='X')
```



parse_args()方法的返回值为namespace，可以用vars()内建函数化为字典

```
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument('--foo')
>>> args = parser.parse_args(['--foo', 'BAR'])
>>> vars(args)
{'foo': 'BAR'}
```





### 多参数共存

```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
parser.add_argument("-v", "--verbosity", action="count", default=0)
args = parser.parse_args()
answer = args.x**args.y
if args.verbosity >= 2:
    print "Running '{}'".format(__file__)
if args.verbosity >= 1:
    print "{}^{} ==".format(args.x, args.y),
print answer

>>>
$ python prog.py 4 2
16
$ python prog.py 4 2 -v
4^2 == 16
$ python prog.py 4 2 -vv
Running 'prog.py'
4^2 == 16
```



### 参数冲突

add_mutually_exclusive_group() 它可以让我们指定某个参数和其他参数冲突。

```
import argparse

parser = argparse.ArgumentParser()
group = parser.add_mutually_exclusive_group()
group.add_argument("-v", "--verbose", action="store_true")
group.add_argument("-q", "--quiet", action="store_true")
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
args = parser.parse_args()
answer = args.x**args.y

if args.quiet:
    print answer
elif args.verbose:
    print "{} to the power {} equals {}".format(args.x, args.y, answer)
else:
    print "{}^{} == {}".format(args.x, args.y, answer)
    
>>>
$ python prog.py 4 2
4^2 == 16
$ python prog.py 4 2 -q
16
$ python prog.py 4 2 -v
4 to the power 2 equals 16
$ python prog.py 4 2 -vq
usage: prog.py [-h] [-v | -q] x y
prog.py: error: argument -q/--quiet: not allowed with argument -v/--verbose
$ python prog.py 4 2 -v --quiet
usage: prog.py [-h] [-v | -q] x y
prog.py: error: argument -q/--quiet: not allowed with argument -v/--verbose
```







# example

## 子命令

```python
import os
import argparse
from git.cmd import Git


def cli():
    """
    git 命名程序入口
    """
    parser = argparse.ArgumentParser(prog='git')
    subparsers = parser.add_subparsers(
        title='These are common Git commands used in various situations',
        metavar='command')

    # status
    status_parser = subparsers.add_parser(
        'status', 
        help='Show the working tree status')
    status_parser.set_defaults(handle=handle_status)
    
    # add
    add_parser = subparsers.add_parser(
        'add', 
        help='Add file contents to the index')
    add_parser.add_argument(
        'pathspec',
        help='Files to add content from', 
        nargs='*')
    add_parser.set_defaults(handle=handle_add)

    # commit
    commit_parser = subparsers.add_parser(
        'commit', 
        help='Record changes to the repository')
    commit_parser.add_argument(
        '--message', '-m', 
        help='Use the given <msg> as the commit message',
        metavar='msg', 
        required=True)
    commit_parser.set_defaults(handle=handle_commit)

    # push
    push_parser = subparsers.add_parser('push', help='Update remote refs along with associated objects')
    push_parser.set_defaults(handle=handle_push)

    # parse args
    git = Git(os.getcwd())
    args = parser.parse_args()
    if hasattr(args, 'handle'):
        args.handle(git, args)
    else:
        parser.print_help()


def handle_status(git, args):
    """
    处理 status 命令
    """
    cmd = ['git', 'status']
    output = git.execute(cmd)
    print(output)


def handle_add(git, args):
    """
    处理 add 命令
    """
    cmd = ['git', 'add'] + args.pathspec
    output = git.execute(cmd)
    print(output)


def handle_commit(git, args):
    """
    处理 -m <msg> 命令
    """
    cmd = ['git', 'commit', '-m', args.message]
    output = git.execute(cmd)
    print(output)


def handle_push(git, args):
    """
    处理 push 命令
    """
    cmd = ['git', 'push']
    output = git.execute(cmd)
    print(output)


if __name__ == '__main__':
    cli()
```

