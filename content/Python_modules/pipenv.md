---
title: "pipenv"
date: 2018-07-31 12:14
---

[TOC]

# pipenv

Pipenv是一款旨在将所有包管理工具（如bundler, composer, npm, cargo, yarn等）的优点集中应用于python领域中的工具。它对各个平台都有很好的支持。



## 特点

- 不用再单独使用`pip`和`virtualenv`, 现在它们合并在一起了
- 不用再维护`requirements.txt`, 使用`Pipfile`和`Pipfile.lock`来代替
- 可以使用多个python版本(`python2`和`python3`)
- 通过查找*Pipfile*，递归的自动查找你的项目
- 在安装了`pyenv`的条件下，可以自动安装需要的Python版本
- 如果 .env 文件存在，则自动载入



## 安装

### Mac

`brew install pipenv`

`brew upgrade pipenv`

### Linux 

`pip3 install --user pipenv`

> This does a [user installation](https://pip.pypa.io/en/stable/user_guide/#user-installs) to prevent breaking any system-wide packages. If `pipenv`isn’t available in your shell after installation, you’ll need to add the [user base](https://docs.python.org/3/library/site.html#site.USER_BASE)’s binary directory to your `PATH`.

` pip install --user --upgrade pipenv` 

`export PATH="/Users/xhxu/Library/Python/3.6/bin:$PATH"`  # 把用户目录下bin放在最前面，这样可以直接使用pipenv了 



#### curl 

`curl https://raw.githubusercontent.com/kennethreitz/pipenv/master/get-pipenv.py | python`



## 卸载

卸载所有

`pipenv uninstall --all`

### 指定依赖

```
pipenv uninstall `pipenv graph --json |python3 depends.py requests`
```

> 其中depends.py脚本会解析依赖关系，排除其他包依赖的项目然后删除





## 使用



### 初始化

```
mkdir test_pipenv
cd test_pipenv
pipenv install # 创建一个虚拟环境
```

>  Pipfile和Pipfile.lock两个文件互相配合，完成虚拟环境的管理工作
>
> ```
> pipenv install --three # 需要确保系统中存在python3版本
> ```



* `pipenv --three`可以初始化一个`python3`版本的虚拟环境

```
xhxu-mac:test_pipenv1 xhxu$ pipenv --three
Creating a virtualenv for this project...
Pipfile: /Users/xhxu/test_pipenv1/Pipfile
Using /Users/xhxu/.pyenv/versions/django_zmrenwu_env3.5.2/bin/python3 (3.5.2) to create virtualenv...
⠋Running virtualenv with interpreter /Users/xhxu/.pyenv/versions/django_zmrenwu_env3.5.2/bin/python3
Using base prefix '/Users/xhxu/.pyenv/versions/3.5.2'
New python executable in /Users/xhxu/.local/share/virtualenvs/test_pipenv1-ckTi0WZI/bin/python3
Also creating executable in /Users/xhxu/.local/share/virtualenvs/test_pipenv1-ckTi0WZI/bin/python
Installing setuptools, pip, wheel...done.
Setting project for test_pipenv1-ckTi0WZI to /Users/xhxu/test_pipenv1

Virtualenv location: /Users/xhxu/.local/share/virtualenvs/test_pipenv1-ckTi0WZI
Creating a Pipfile for this project...
```



- `pipenv --two`可以初始化一个`python2`版本的虚拟环境

```
xhxu-mac:test_pipenv2 xhxu$ pipenv --two
Creating a virtualenv for this project...
Pipfile: /Users/xhxu/test_pipenv2/Pipfile
Using /Users/xhxu/.pyenv/versions/env2.7.13/bin/python2 (2.7.13) to create virtualenv...
⠋Running virtualenv with interpreter /Users/xhxu/.pyenv/versions/env2.7.13/bin/python2
Using real prefix '/Users/xhxu/.pyenv/versions/2.7.13'
New python executable in /Users/xhxu/.local/share/virtualenvs/test_pipenv2-pBEJdovF/bin/python2
Also creating executable in /Users/xhxu/.local/share/virtualenvs/test_pipenv2-pBEJdovF/bin/python
Installing setuptools, pip, wheel...done.
Setting project for test_pipenv2-pBEJdovF to /Users/xhxu/test_pipenv2

Virtualenv location: /Users/xhxu/.local/share/virtualenvs/test_pipenv2-pBEJdovF
Creating a Pipfile for this project...
```



指定具体版本

```
pipenv --python 3.7.0
```



#### Pipfile & Pipfile.lock

Pipfile 存放着当前虚拟环境的配置信息，包含python版本，pypi源，以及项目安装的依赖库

> `pipenv`根据这个来寻找项目的根目录。
>
> `Pipfile` 文件是 `TOML` 格式而不是 `requirements.txt` 那样的纯文本。[
>
> 一个项目对应一个 Pipfile，支持开发环境与正式环境区分。默认提供 `default` 和 `development` 区分



Pipfile.lock 顾名思义，这个文件时对于`Pipfile`的一个锁定。支持锁定项目不同的版本所依赖的环境.





#### 自动补齐

```
eval "$(pipenv --completion)"
```



### Virtualenv

Linux 默认会在`~/.local/share/virtualenvs/` 下面创建

在项目目录内创建虚拟环境文件夹，可以设置环境变量`PIPENV_VENV_IN_PROJECT` ，名为`.venv` 的虚拟环境文件夹将在项目根目录被创建






#### 激活 Virtualenv

`pipenv shell`

```
xhxu-mac:test_pipenv xhxu$ pipenv shell
Launching subshell in virtual environment…
bash-3.2$  . /Users/xhxu/.local/share/virtualenvs/test_pipenv-v79hOH8k/bin/activate
(test_pipenv-v79hOH8k) bash-3.2$ which python3
/Users/xhxu/.local/share/virtualenvs/test_pipenv-v79hOH8k/bin/python3
```



#### 退出 Virtualenv 

`exit`

```
(test_pipenv-v79hOH8k) bash-3.2$ exit
exit
xhxu-mac:test_pipenv xhxu$ which python3
/Users/xhxu/.pyenv/shims/python3
```



#### 自定义虚拟环境的路径

默认情况下,`pipenv`使用[`pew`](https://github.com/berdario/pew)来管理虚拟环境的路径，我们可以自定义`WORKON_HOME`环境变量来设置虚拟环境的路径。比如:

```
export WORKON_HOME=~/.venvs
```

> 可以通过社会环境变量`PIPENV_VENV_IN_PROJECT`使虚拟环境在每个项目的根目录下`project/.venv`。





#### 指定安装包的版本

`pipenv install requests==2.13.0 `

> 这个命令也会自动更新`Pipfile`文件



#### 指定安装包的源

如果我们需要在安装包时，从一个源下载一个安装包，然后从另一个源下载另一个安装包，我们可以通过下面的方式配置

```
[[source]]
url = "https://pypi.python.org/simple"
verify_ssl = true
name = "pypi"

[[source]]
url = "http://pypi.home.kennethreitz.org/simple"
verify_ssl = false
name = "home"

[dev-packages]

[packages]
requests = {version="*", index="home"}
maya = {version="*", index="pypi"}
records = "*"
```





#### --json

以 json形式输出



### autoenv 自动激活虚拟环境

```
pip install autoenv  # 安装 autoenv
```

`autoenv` 可以在进入项目之后自动检测项目目录的 `.env` 文件激活项目所需的虚拟环境



```
vim .env
```

```
# bash 的话，执行这一条指令
echo "source `which activate.sh`" >> ~/.bashrc
# zsh 的话，执行这一条指令
echo "source `which activate.sh`" >> ~/.zshrc
```





## pipenv 命令参数

### run 虚拟环境运行 

即不显示激活虚拟环境即可在当前项目的虚拟环境中执行命令

`pipenv run which python `

pipenv run 可以激活虚拟环境，并使用shell命令

```
xhxu-mac:test_pipenv xhxu$ pipenv run python manage.py runserver
xhxu-mac:test_pipenv xhxu$ pipenv run python your_script.py
```

如果你不想每次运行Python时都输入这么多字符，可以在shell中设置一个别名，例如，

```
alias prp="pipenv run python"
```



### check 安全性检查

`pipenv check`

```
xhxu-mac:test_pipenv xhxu$ pipenv check
Checking PEP 508 requirements...
Passed!
Checking installed package safety...
All good!
```



### graph 图形化依赖

将输出一个包含当前所有已安装依赖的漂亮图形

```
$ pipenv graph
elasticsearch-dsl==6.2.1
  - elasticsearch [required: >=6.0.0,<7.0.0, installed: 6.3.0]
    - urllib3 [required: >=1.21.1, installed: 1.23]
  - ipaddress [required: Any, installed: 1.0.22]
  - python-dateutil [required: Any, installed: 2.7.3]
    - six [required: >=1.5, installed: 1.11.0]
  - six [required: Any, installed: 1.11.0]
requests==2.19.1
  - certifi [required: >=2017.4.17, installed: 2018.4.16]
  - chardet [required: >=3.0.2,<3.1.0, installed: 3.0.4]
  - idna [required: >=2.5,<2.8, installed: 2.7]
  - urllib3 [required: >=1.21.1,<1.24, installed: 1.23]
```





### install 安装包

`pipenv install PACKAGE_NAME`

```
$ pipenv install elasticsearch-dsl requests
```



指定版本


```
$ pipenv install --python 3
$ pipenv install --python 3.6
$ pipenv install --python 2.7.14
```



### shell 补全

如果使用的是`bash`, 可添加下面语句到`.bashrc`或`.bash_profile`

```
eval "$(pipenv --completion)"
```

使用fish shell，将下列命令加入`~/.config/fish/completions/pipenv.fish`

```
eval (pipenv --completion)
```



### Lock 锁定

```
$ pipenv lock
Locking [dev-packages] dependencies...
Locking [packages] dependencies...
Updated Pipfile.lock (55ac43)!
```





### --where 定位项目 

```
$ pipenv --where
/Users/xhxu/test_pipenv
```



### --venv 定位虚拟环境

```
$ pipenv --venv
/Users/kennethreitz/.local/share/virtualenvs/test-Skyy4vr
```



### --envs 定义环境变量

pipenv内置了很多环境变量，可以通过设置这些环境变量来配置`pipenv`

```
$ pipenv --envs
The following environment variables can be set, to do various things:

  - PIPENV_MAX_DEPTH
  - PIPENV_SHELL
  - PIPENV_DOTENV_LOCATION
  - PIPENV_HIDE_EMOJIS
  - PIPENV_CACHE_DIR
  - PIPENV_VIRTUALENV
  - PIPENV_MAX_SUBPROCESS
  - PIPENV_COLORBLIND
  - PIPENV_VENV_IN_PROJECT # 在项目根路径下创建虚拟环境
  - PIPENV_MAX_ROUNDS
  - PIPENV_USE_SYSTEM
  - PIPENV_SHELL_COMPAT
  - PIPENV_USE_HASHES
  - PIPENV_NOSPIN
  - PIPENV_PIPFILE
  - PIPENV_INSTALL_TIMEOUT
  - PIPENV_YES
  - PIPENV_SHELL_FANCY
  - PIPENV_DONT_USE_PYENV
  - PIPENV_DONT_LOAD_ENV
  - PIPENV_DEFAULT_PYTHON_VERSION # 设置创建虚拟环境时默认的python版本信息，如: 3.6
  - PIPENV_SKIP_VALIDATION
  - PIPENV_TIMEOUT
```

需要修改某个默认配置时，只需要把它添加到`.bashrc`或`.bash_profile`文件里即可。



### --py 定位Python解释器

```
$ pipenv --py
/Users/kennethreitz/.local/share/virtualenvs/test-Skyy4vre/bin/python
```



### --python 指定版本 

```
xhxu-mac:test_pipenv2 xhxu$ pipenv --python 2.7.11
```

> `pipenv`会自动扫描系统寻找合适的版本信息，如果找不到的话，同时又安装了`pyenv`, 它会自动调用`pyenv`下载对应的版本的python

Create a new project using Python 3.6, specifically:
`$ pipenv --python 3.6`



### --man 帮助

`pipenv --man`



### 操作 requirements.txt 


#### 导入

当在执行`pipenv install`命令的时候，如果有一个`requirements.txt`文件，那么会自动从`requirements.txt`文件导入安装包信息并创建一个`Pipfile`文件。

同样可以使用`$ pipenv install -r path/to/requirements.txt`来导入`requirements.txt`文件

> 默认情况下，我们都是在`requirements.txt`文件里指定了安装包的版本信息的，在导入`requirements.txt`文件时，版本信息也会被自动写`Pipfile`文件里， 但是这个信息我们一般不需要保存在`Pipfile`文件里，需要手动更新`Pipfile`来删除版本信息



#### 生成

我们也可以从`Pipfile`和`Pipfile.lock`文件来生成`requirements.txt`

```
# 生成requirements.txt文件
$ pipenv lock -r

# 生成dev-packages的requirements.txt文件
# pipenv lock -r -d
```



### 浏览模块代码

```
# 用编辑器打开requests模块
$ pipenv open requests
```



### 自动加载环境变量.env

如果项目根目录下有`.env`文件，`$ pipenv shell`和`$ pipenv run`会自动加载它。

```
$ cat .env
HELLO=WORLD

$ pipenv run python
Loading .env environment variables…
Python 2.7.13 (default, Jul 18 2017, 09:17:00)
[GCC 4.2.1 Compatible Apple LLVM 8.1.0 (clang-802.0.42)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import os
>>> os.environ['HELLO']
'WORLD'
```

 















### 浏览模块代码

```
# 用编辑器打开requests模块
$ pipenv open requests
```





### 更新 pypi源来提高依赖库安装的速度

在使用pipenv的时候，常常会在安装的时候，一直卡在了 `Locking` 这里，通过加上 `-v` 参数，可以看到安装过程中的步骤信息，卡在了下载那里，这时应该可以意识到是因为网络的原因，pipenv创建的 `Pipfile` 中默认的pypi源是python官方的 **https://pypi.python.org/simple**。我们国内用户访问下载的时候会很慢。

所以，我一般会在创建好Pipfile以后，修改到文件中 `source` 块下的 `url` 字段，设置为国内的 pypi 源就好了，我推荐的是清华的pypi源，具体设置如下：

*备注：我还没有找到如何修改能在创建时就设好的方法，应该修改源码是可以的，但这样不尊重源码，毕竟高墙是我们自己筑起的，如果有好的方法，您不妨在评论中告诉我一下*

```
[[source]]

url = "https://pypi.tuna.tsinghua.edu.cn/simple"
verify_ssl = true
name = "pypi"
```





## Commands

Install all dependencies for a project (including dev):
`$ pipenv install --dev`

Create a lockfile containing pre-releases:
`$ pipenv lock --pre`

Show a graph of your installed dependencies:
`$ pipenv graph`

Check your installed dependencies for security vulnerabilities:
`$ pipenv check`

Install a local setup.py into your virtual environment/Pipfile:
`$ pipenv install -e .`

Use a lower-level pip command:
`$ pipenv run pip freeze`





