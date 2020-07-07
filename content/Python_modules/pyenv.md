---
title: "pyenv"
date: 2019-01-26 12:19
---


[TOC]



# pyenv



## Installation


### mac

```
ruby -e "$(curl -fsSL <https://raw.githubusercontent.com/Homebrew/install/master/install>)" 
```

**Homebrew on Mac OS X**

You can also install pyenv using the [Homebrew](http://brew.sh/) package manager for Mac OS X.



```
$ brew update

$ brew install pyenv
```

To upgrade pyenv in the future, use upgrade instead of install.



After installation, you'll need to add eval "$(pyenv init -)" to your profile (as stated in the caveats displayed by Homebrew — to display them again, use brew info pyenv). You only need to add that to your profile once.



Then follow the rest of the post-installation steps under "Basic GitHub Checkout" above, starting with #4 ("restart your shell so the path changes take effect").



**Advanced Configuration**

Skip this section unless you must know what every line in your shell profile is doing.



pyenv init is the only command that crosses the line of loading extra commands into your shell. Coming from rvm, some of you might be opposed to this idea. Here's what pyenv init actually does:



1. **Sets up your shims path.** This is the only requirement for pyenv to function properly. You can do this by hand by prepending ~/.pyenv/shims to your $PATH.

   

2. **Installs autocompletion.** This is entirely optional but pretty useful. Sourcing ~/.pyenv/completions/pyenv.bash will set that up. There is also a ~/.pyenv/completions/pyenv.zsh for Zsh users.

   

3. **Rehashes shims.** From time to time you'll need to rebuild your shim files. Doing this on init makes sure everything is up to date. You can always run pyenv rehash manually.

   

4. **Installs the sh dispatcher.** This bit is also optional, but allows pyenv and plugins to change variables in your current shell, making commands like pyenv shell possible. The sh dispatcher doesn't do anything crazy like override cd or hack your shell prompt, but if for some reason you need pyenv to be a real script rather than a shell function, you can safely skip it.

   

To see exactly what happens under the hood for yourself, run pyenv init -.



**Uninstalling Python Versions**

As time goes on, you will accumulate Python versions in your ~/.pyenv/versions directory.



To remove old Python versions, pyenv uninstall command to automate the removal process.



Alternatively, simply rm -rf the directory of the version you want to remove. You can find the directory of a particular Python version with the pyenv prefix command, e.g. pyenv prefix 2.6.8.



#### virtualenv

**Installing with Homebrew (for OS X users)**

Mac OS X users can install pyenv-virtualenv with the [Homebrew](http://brew.sh/) package manager. This will give you access to the pyenv-virtualenv command. If you have pyenv installed, you will also be able to use the pyenv virtualenv command.



```
$ brew install pyenv-virtualenv
```

```
$ brew install --HEAD pyenv-virtualenv
```

```
Cat ~/.bash_profile 
eval "$(pyenv init -)" 
eval "$(pyenv virtualenv-init -)" 
```

> to your profile (as stated in the caveats). You'll only ever have to do this once.



### Linux

安装 安装 git：

```
yum install git
```



克隆 pyenv 至本地：

```
git clone git://github.com/yyuu/pyenv.git .pyenv
git clone https://github.com/pyenv/pyenv-virtualenv.git
```



为 pyenv 提供环境配置

```
echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc

echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc

echo 'eval "$(pyenv init -)"' >> ~/.bashrc
echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.bashrc
```



重启 shell 进程，以使得新配置生效

```
exec   $SHELL
source ~/.bash_profile
```



#### 非root用户

```
export PATH=~/.pyenv/shims:~/.pyenv/bin:"$PATH"
```



#### centos

```
yum -y install gcc make patch gdbm-devel openssl-devel sqlite-devel readline-devel zlib-devel bzip2-devel
```



```
useradd python

curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```



```
export PATH="/home/python/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
source ~/.bashrc
```



#### ubuntu

```
apt-get update && sudo apt-get upgrade
apt install build-essential checkinstall zlib1g-dev libssl-dev -y


apt-get install build-essential libsqlite3-dev sqlite3 bzip2 libbz2-dev zlib1g-dev libssl-dev openssl libgdbm-dev libgdbm-compat-dev liblzma-dev libreadline-dev libncursesw5-dev libffi-dev uuid-dev libffi6
```



```
useradd python

curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash
```



```
export PATH="/home/python/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"
source ~/.bashrc
```



#### 





## args

### Install --list 所有版本

查看可安装的 python 版本列表

```
# pyenv install -list
```



### verions 查看安装版本

查看已安装的版本：

```
# pyenv versions
*     system (set by /root/.pyenv/version)
```



### global 全局设置

查看所有受到pyenv 控制的窗口，不建议在root用户下使用，会影响本地Python版本



### shell 会话设置

只作用于当前会话



### local 本地设置

使用pyenv local 设置从当前工作目录开始向下递归都继承这个设置





### rehash 更新清单

每次安装新的版本后，建议使用 rehash 命令重新 hash 其可用的 python 清单

```
# pyenv     rehash
```



### uninstall 卸载

```
pyenv uninstall my-virtual-env
```





## Jenkins 配置

选择`Execute shell`，添加构建代码

```
PROJECT_NAME=testproject
PYENV_HOME=$HOME/.pyenv
PROJECT_ENV=$PYENV_HOME/versions/$PROJECT_NAME
PYTHON_VER_FILE=$WORKSPACE/.python-version
export PATH=$PYENV_HOME/bin/:$PYENV_HOME/shims:$PATH

#Delete previously built virtualenv
if [ -d $PROJECT_ENV ]; then
    pyenv uninstall -f $PROJECT_NAME
    if [ -f $PYTHON_VER_FILE ]; then
        rm -f $PYTHON_VER_FILE
    fi
fi

pyenv virtualenv 3.6.0 $PROJECT_NAME
pyenv local $PROJECT_NAME
pip install --quiet nosexcover
```

