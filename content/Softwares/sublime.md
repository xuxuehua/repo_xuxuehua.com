---
title: "sublime"
date: 2018-12-18 20:03
---


[TOC]



# sublime



## ActualVim

先安装依赖NeoVim

下载并解压到任意位置 https://github.com/neovim/neovim/releases

添加到环境变量

```
vim ~/.bash_profile
export PATH="/Users/rxu/coding/nvim-osx64/bin:$PATH"
```



安装ActualVim

Ctrl+Shift+P 安装package control

Ctrl+Shift+P  install package 

弹出选项选择 ActualVim



重启Sublime后，完美的Vim已经可以正常使用了





## Vintage Mode (Enable VIM) (Not too  recommended)

Preferences -> Settings	

```
{
	"font_size": 17,
	"ignored_packages": [],
	"vintage_start_in_command_mode": true,
}
```



## Enable UTF-8

调出console

```
ctrl + ` 
```



install package 选项， 然后输入ConvertToUTF8







## Installation

### Ubuntu

Install the GPG key:

```
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -
```

Ensure apt is set up to work with https sources:

```
sudo apt-get install apt-transport-https
```

Select the channel to use:

Stable

```
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list 
```



Dev

```
echo "deb https://download.sublimetext.com/ apt/dev/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
```



Update apt sources and install Sublime Text

```
sudo apt-get -y update
sudo apt-get -y install sublime-text
```



### CentOS

Install the GPG key:

```
sudo rpm -v --import https://download.sublimetext.com/sublimehq-rpm-pub.gpg
```

Select the channel to use:

Stable

```
sudo yum-config-manager --add-repo https://download.sublimetext.com/rpm/stable/x86_64/sublime-text.repo
```



Dev

```
sudo yum-config-manager --add-repo https://download.sublimetext.com/rpm/dev/x86_64/sublime-text.repo
```

Update yum and install Sublime Text

```
sudo yum install sublime-text
```